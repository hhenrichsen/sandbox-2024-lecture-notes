# Lecture 15 - Q&A + Code Organization

<!-- START doctoc generated TOC please keep comment here to allow auto update -->

- [Lecture 15 - Q&A + Code Organization](#lecture-15---qa--code-organization)
  - [Links](#links)
  - [Events](#events)
  - [News](#news)
  - [Code Organization](#code-organization)
    - [Data / Model](#data--model)
    - [Business / Domain](#business--domain)
    - [View / Presentation](#view--presentation)
  - [Design Patterns](#design-patterns)
    - [Incremental (or Exponential) Falloff](#incremental-or-exponential-falloff)
      - [Sample Implementation](#sample-implementation)
    - [Debounce](#debounce)
      - [Sample Implementation](#sample-implementation-1)
      - [Sample (React) Implementation](#sample-react-implementation)
    - [Service Locator / Dependency Injection](#service-locator--dependency-injection)
    - [Feature Flags](#feature-flags)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Links

- [Feedback Form](https://docs.google.com/forms/d/e/1FAIpQLSdcu-u0LD5kB9rhOcA7E1ZCw6w05RlejzrFrRALEz7krkLjVQ/viewform?usp=sf_link)
- [Q&A Form](https://docs.google.com/forms/d/e/1FAIpQLSd4c3JqKFSybays7xUNk3EeiUaDak7XvRqRyosng0ATCZf2bQ/viewform?usp=sf_link)
- [Office Hours](https://calendly.com/hhenrichsen)

## Events

- [HackUSU](https://huntsman.usu.edu/hackusu/) - February 28th - March 1st

## News

## Code Organization

There are lots of code organization theories out there. Here are some of them:

- [Bob Martin's Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
  and
  [Screaming Architecture](https://blog.cleancoder.com/uncle-bob/2011/09/30/Screaming-Architecture.html)
- [Martin Fowler's Presentation-Domain-Data Layering](https://martinfowler.com/bliki/PresentationDomainDataLayering.html)
- [12 Factor Apps](https://12factor.net/) by Adam Wiggins

There are many more than these than the ones I've listed. Everyone has an
opinion here.

Ultimately, I've found some concepts that were useful and stuck when I was first
building a project, some concepts that got useful and stuck when I was
refactoring a project to work better, some concepts that have only worked on a
large scale like the code I work on at Lucid, and some concepts that I frankly
don't understand or care about yet in each of these.

**Also, real code is messy**. Sometimes stuff gets created in the wrong place.
Sometimes something toes the line between categories. The time your code is
fully organized is several years after you stop adding features (and even then,
it may not be).

### Data / Model

This layer is things like your data access objects (sometimes called DAOs),
repositories, clients, and data transfer objects (sometimes called DTOs). This
will include things that call out to third parties, whether that third party is
a database, or some other client.

I find when I'm getting started, some of these concepts are useful, and some of
them are not. For my infection tag app, some of these are fairly simple, just
declarations of data types and potentially type guards, depending on the
language you choose:

```ts
import z from "zod";

interface Team {
  name: string;
  gameId: string;
  points: number;
  modified: Date;
}

const TeamValidator = z.object({
  content: z.string(),
  gameId: z.string(),
  points: z.number(),
  modified: z.date(),
});
```

These will have some relation to my Domain as well. Sometimes it makes sense to
split the classes between the layers, but that will vary based on the scale of
your project and the types of problems that you're running into.

For example, most of the time I don't need to query all of a User's memberships
from the database and I just am interested in their name, or potentially a
single membership. In this case, I likely have not only a User DAO that
represents the state of one query, but multiple that I can get access to from my
data layer. Here's a stripped down example of this:

```ts
interface ShallowUser {
  id: string;
  name: string;
  email: string;
}

interface FullUser {
  id: string;
  name: string;
  email: string;
  games: GameMembership[];
}

interface GameMembership {
  user: ShallowUser;
  game: ShallowGame;
  team: ShallowTeam;
}
```

I also have third party clients; I need to get user info from Discord and
Google, so I need DAOs for those. It may not be database access, but I try to
think of my database as a third party just like any other third party.

```ts
interface OAuthUserInfo {
  id: string;
  email: string;
  verified: string;
}

// Currently left as just extensions, but both providers give back
// different information that may be useful in the future.
interface DiscordUserInfo extends OAuthUserInfo {}

interface GoogleUserInfo extends OAuthUserInfo {}
```

These will also include my functions or classes to get access to the data,
perhaps a collection of functions:

```ts
function userFromResult(row: Record<string, unknown>) {
  // Whatever parsing, mapping, etc.
}

export function getUserById(id: string): Promise<ShallowUser | undefined> {
  const result = await db.from("users").select("*").where("id", id);
  return userFromResult(result);
}
```

Or maybe a service class:

```ts
class UserRepository {
  private readonly connection = inject(DatabaseConnection);

  private parseUser(row: Record<string, unknown>) {
    // Whatever parsing, mapping, etc.
  }

  public async getUserById(id: string): Promise<ShallowUser | undefined> {
    const result = await this.connection
      .from("users")
      .select("*")
      .where("id", id);
    return this.parseUser(result);
  }
}
```

This will depend on project structure, normally. In a long-lived service, I find
it more useful to create all of the services I'll need and keep them loaded for
the lifetime of the service. For a shorter-lived service (like a lambda, or a
Next.js project), I tend to opt for functions since that way I don't waste
memory, but I likely end up doing more work to put together everything I need
for the one-off request.

Some architectures will have different opinions about where each of these things
belong. I think the data layer is the core and should be built upon by other
things, and that that keeps it straightforward enough until you need the
flexibility of other approaches. Other approaches say that data is just one
application of the domain

### Business / Domain

This is where the business logic lives. I think most functions that your app can
do should be isolated into reusable chunks of logic. It's very easy to write a
controller like this:

```tsx
export default function Page({}) {
	// Read from DB
	const result = await db.from('notes').select('*').limit(20);

	// Process data
	const notes = result.map((rawNote) => ({
		title: rawNote.title,
		content: rawNote.content
	}));

	// some business logic
	const parser = new MarkdownParser();
	const processedNotes = notes.map((note) => {
		const htmlContent = parser.parse(note.content);
		return {
			...note,
			content: htmlContent
		};
	});

	// If I really wanted to do something crazy, I'd implement
	// truncation for HTML right here. Unfortunately I'm not
	// do that.

	// Render
	return (<main>{processedNotes.map((note) => (<article>
		<h2>{note.title}</h2>
		<div dangerouslySetInnerHTML={{__html: note.content}}></div>
	</article>))
}
```

This is very fast to write for the first time, and is really good for
prototyping, showing off framework features, and making short-to-medium-form
YouTube content. But I often find that things like this lead to doing this in
_every_ controller, and that turning into needing to make changes in many places
to update things.

Your domain layer has your business logic, and any data that's core to that.
This is the place where data should flow from one place to another, and should
be isolated from the model and view layers.

Bob Martin splits this into two layers, the Application Business Rules and
Interface Adapters. Application Business Rules are your business logic, where
data is mutated and flows from one place to another. Interface Adapters take
that data and translate it into forms that the view layer(s) will use.

### View / Presentation

View is not UI. UI is View, but View can also be API endpoints, or a chatbot, or
a third party integration, or any number of other things. Your view is where the
consumer (not necessarily user) gets the information they want.

For a backend service, this is your API most of the time. For an SDK, this might
be the public interface that users can use to build off of your software. This
can also be your UI, and indeed in the frontend your UI is generally the View
layer.

I'm of the opinion that the View layer should be a thin interface to the domain
layer if possible. The domain layer should deal with getting the data into the
form I want it, and the view should display it.

## Design Patterns

### Incremental (or Exponential) Falloff

Sometimes there are brief issues in making requests, especially when a server is
under high load, or on unstable network connections. The default response is to
fail those requests as soon as they fail once, but sometimes it makes sense to
try again a few times and see if there really is a big problem.

A normal way of doing this is to introduce a number of increasing delays before
"really" failing a request. This is easier when the requests are idempotent (or
in other words, multiple requests will not cause multiple actions to happen).

**tl;dr**: Something fails, try again later. If it still fails, try again later
later.

#### Sample Implementation

```ts
export function withFalloff<R>(
	f: () => Promise<R>,
	delays: number[] = [1_000, 4_000, 9_000, 16_000]
): Promise<R> {
	return new Promise(async (resolve, reject) => {
		for (const delay : delays) {
			try {
				// Try running the request and returning the result
				const result = await f();
				resolve(result);
				return;
			}
			catch (_) {
				// If we fail, wait `delay` ms and then try again
				await new Promise(
					(resolve) => setTimeout(() => resolve(), delay)
				);
			}
		}
		// If we fail for each provided delay, fail the request.
		reject();
	});
}
```

A more complex version of this can build into the
[Circuit Breaker pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/circuit-breaker).

### Debounce

Debounce is super useful in UIs, especially when users might do things quicker
than we want to do them. For example, checking a username's availability takes a
database query. If we're doing that as a user is typing, we probably don't want
each keypress to check a username, but instead to only check once they're done.
Or in the case I talked about last time, we might want to only record a response
after a short delay where people might change their mind.

To solve this, we introduce a brief delay before doing an expensive operation.
This can make sense to do on network requests in response to UI, or other
even-more expensive operations.

**tl;dr**: Only send a response when we're really sure about it.

#### Sample Implementation

```ts
type TimeoutHandle = ReturnType<typeof setTimeout>;

export function withDebounce(f: () => void, delay: number = 2_500) {
  // Keep the last invocation around
  let timeout: TimeoutHandle | undefined = undefined;

  // Hand back a new function
  return function () {
    // That clears the previous invocation if it exists
    if (timeout) {
      clearTimeout(timeout);
    }
    // And stores the new invocation, running it after `delay`
    timeout = setTimeout(() => {
      f();
      timeout = undefined;
    }, delay);
  };
}
```

#### Sample (React) Implementation

```tsx

```

### Service Locator / Dependency Injection

I normally give a whole talk on this at HackUSU. I might do it again this year,
we'll see.

Dependency Injection is the practice of providing dependencies to services,
rather than requiring services to find or construct their own dependencies. For
example:

```ts
class UserService {
  constructor(
    private readonly connection: DatabaseConnection,
    private readonly environment: Environment
  ) {}
}
```

A service locator is a similar concept, where services are registered (and
potentially constructed) in a single location, and then other things can depend
on them:

```ts
class UserService {
  private readonly connection = inject(DatabaseConnection);
  private readonly environment = inject(Environment);
}
```

Both of these have a lot of overlap, and most things that implement one have
some concept of the other.

One might ask "why would you do this?", and my answer is that it makes code
significantly more flexible. You can run code in different environments, or run
different code only according to configuration. The main way to do this is by
building to service interfaces, rather than specific services. So I can ask for
a `UserRepository`, but that might be a `DatabaseUserRepository` or a
`MemoryUserRepository` or an `AIHallucinatedUserRepository` depending on the
implementation that I register to my service locator.

Turns out being able to run a `MemoryUserRepository` is really useful for tests,
and being able to use a generic `UserRepository` in my business layer makes my
code more resilient to future changes in service. I might write a new
`CachedUserRepository` that wraps another `UserRepository` and provide that
instead.

Some project structures will yield themselves more to this model than others;
more serverless models tend to not like this as much and instead prefer asking
for the things needed for individual requests. I think this is a useful tool to
have and understand as a codebase grows, though.

### Feature Flags

Being able to turn features on and off is useful. There are lots of ways to
implement this in a product; some choose to store it attached to users in a
database, some prefer cookies that show which flags are enabled, and some just
pass it off to third parties. I prefer the lattermost of those options for
projects that I'm tinkering with, but the concept of being able to choose who
sees which experience is useful both in a data collection way as well as a risk
mitigation way.
