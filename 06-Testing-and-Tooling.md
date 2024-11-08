# Lecture 06 - Testing and Tooling

## Pre-Lecture

### Links

- [Feedback Form](https://docs.google.com/forms/d/e/1FAIpQLSdcu-u0LD5kB9rhOcA7E1ZCw6w05RlejzrFrRALEz7krkLjVQ/viewform?usp=sf_link)
- [Q&A Form](https://docs.google.com/forms/d/e/1FAIpQLSd4c3JqKFSybays7xUNk3EeiUaDak7XvRqRyosng0ATCZf2bQ/viewform?usp=sf_link)
- [Office Hours](https://calendly.com/hhenrichsen)

### Events and Announcements

_I mostly know about Lucid events, but feel free to slack me a day or two before
class and I can get things added here._

- Lucid Programming Competition - October 26th, 2024

### News

### Articles

## Review from Last Time

### Rolling Your Own Auth

Please don't roll your own auth. Please please please don't try to roll your own
encryption.

### Password Salting

Salting is generating a unique random string and appending it to passwords
before they are hashed into the database. These should be unique _per user_, and
should be stored somewhere accessible. The primary point of things like this is
to prevent rainbow table attacks.

You also have to choose an appropriate hashing algorithm when salting to make
sure that you're not making yourself susceptible to birthday attacks.

This is another reason not to roll your own authentication. There's a lot of odd
and niche probability stuff that you have to learn and take into account before
writing even a decent authentication solution, and it also reduces the amount of
valuable information in your database were something to be misconfigured or
leaked in some way.

#### Rainbow Table

Rainbow Tables are an attack that can be used when a database is dumped. Instead
of trying to crack each password to get the plaintext password, you instead
pre-hash a number of common passwords, and then look for any hashed passwords
that match those hashes.

Here's what a very short one of those might look like:

| password    | hash (sha256)                                                    |
| ----------- | ---------------------------------------------------------------- |
| hunter2     | f52fbd32b2b3b86ff88ef6c490628285f482af15ddcb29541f94bcf526a3f6c7 |
| hunter3     | fb8c2e2b85ca81eb4350199faddd983cb26af3064614e737ea9f479621cfa57a |
| password123 | ef92b778bafe771e89245b89ecbc08a44a4e166c06659911881f383d4473e94f |

Now, if you saw this row in the database:

| id                                   | email              | displayname | password                                                         |
| ------------------------------------ | ------------------ | ----------- | ---------------------------------------------------------------- |
| 5573763f-ed72-4c16-a3dc-18ff75af22d8 | hunter@example.com | Hunter      | f52fbd32b2b3b86ff88ef6c490628285f482af15ddcb29541f94bcf526a3f6c7 |

It would be reasonable to assume that you have an email-password pair of
hunter@example.com and hunter2.

Now, if I was salting my passwords, I wouldn't be saving my password to the
database alone. Instead, I'd be saving something like `hunter2M@C7!`, where I
would save `M@C7!` to my user as the salt for their password. This doesn't need
to be encrypted or anything, but makes it much harder to match in a rainbow
table; the amount of work gets much much more, because instead of being able to
do lookups on a database level, I have to create rainbow tables for each row in
the database. That's exponentially more work.

## When to Test

In my opinion, tests are worth investing in once you have people using your
project, especially if they're paying for it. You don't want to accidentally
break the features people already know and are using while you develop new ones.
I've found investing an hour or two saves me that time over and over again the
longer a project goes on, and makes it easier for me to push new features.

## Types of Tests

### Unit

Unit tests are your lowest layer of tests, and should make sure that the _public
interface_ of each unit of logic (normally, classes, files or components)
adheres to some contract. These are also the most simple to implement, since
ideally they test each unit in isolation. These tend to be quick to run and
should be the type of tests you have the most of.

### Integration

"Correctness" is hard to guarantee on some areas, and that's where integration
tests are useful. These take multiple related units and test them together,
normally with some more setup or moving parts involved, and make sure that a
group of units work together as expected. I find that these are much rarer than
unit tests.

### End to End

End to end tests are tests that check the behavior of your full system. Ideally,
they run against your frontend, backend, database, etc. all running in a
semi-production environment, and verify that when everything is put together, it
works together.

These are useful for things like screenshot tests, as well as for making sure
that larger functionality works well together.

## Demo: Unit Tests for Components and Pages

### Components at Test

One of the components I've built is a form like this:

```tsx
"use client";
import { useForm } from "react-hook-form";
import { z } from "zod";
import { zodResolver } from "@hookform/resolvers/zod";
import { Button } from "@/components/ui/button";
import {
  Form,
  FormField,
  FormItem,
  FormLabel,
  FormControl,
  FormMessage,
} from "@/components/ui/form";
import { Input } from "@/components/ui/input";
import { Textarea } from "@/components/ui/textarea";
import { createNoteAction } from "@/app/actions";
import { useRouter } from "next/navigation";
import { useState } from "react";
import { Alert, AlertDescription, AlertTitle } from "@/components/ui/alert";
import { ExclamationTriangleIcon } from "@radix-ui/react-icons";
import { XIcon } from "lucide-react";

const createNoteSchema = z.object({
  title: z.string().min(2).max(50),
  content: z.string(),
});

export default function CreateNote() {
  const router = useRouter();
  const form = useForm<z.infer<typeof createNoteSchema>>({
    resolver: zodResolver(createNoteSchema),
    defaultValues: {
      title: "",
      content: "",
    },
  });

  const [error, setError] = useState<string | null>(null);

  async function onSubmit({
    title,
    content,
  }: {
    title: string;
    content: string;
  }) {
    const noteResponse = await createNoteAction({ title, content });
    if (noteResponse.ok) {
      router.push(`/notes/${noteResponse.id}`);
    } else {
      setError(noteResponse.reason);
    }
  }

  return (
    <div className="flex flex-col flex-1 w-full md:max-w-prose max-w-full gap-4">
      {!!error ? (
        <Alert variant={"destructive"}>
          <ExclamationTriangleIcon className="h-4 w-4" />
          <AlertTitle>Failed to Create Note</AlertTitle>
          <AlertDescription>{error}</AlertDescription>
          <XIcon className="h-4 w-4" onClick={() => setError(null)} />
        </Alert>
      ) : null}
      <Form {...form}>
        <form
          onSubmit={form.handleSubmit(onSubmit)}
          className="space-y-8 w-full"
        >
          <FormField
            control={form.control}
            name="title"
            render={({ field }) => (
              <FormItem>
                <FormLabel data-testid="title-label">Title</FormLabel>
                <FormControl>
                  <Input
                    placeholder="A Note"
                    {...field}
                    data-testid="title-input"
                  />
                </FormControl>
                <FormMessage data-testid="title-message" />
              </FormItem>
            )}
          />
          <FormField
            control={form.control}
            name="content"
            render={({ field }) => (
              <FormItem>
                <FormLabel data-testid="content-label">Content</FormLabel>
                <FormControl>
                  <Textarea
                    data-testid="content-input"
                    placeholder="Something thoughtful, hopefully."
                    {...field}
                  />
                </FormControl>
                <FormMessage data-testid="content-message" />
              </FormItem>
            )}
          />
          <Button type="submit" data-testid="submit-button">
            Submit
          </Button>
        </form>
      </Form>
    </div>
  );
}
```

It uses a server action that looks like this:

```ts
"use server";

import { createClient } from "@/utils/supabase/server";
import { redirect } from "next/navigation";

// Either return an object with ok: false, and an error message, or
// return ok: true and the note contents. ID is guaranteed if the
// note is successful.
export const createNoteAction = async (note: {
  title: string;
  content: string;
}): Promise<
  | { ok: false; reason: string }
  | { ok: true; id: string; title?: string; content?: string }
> => {
  // Get user or redirect
  const supabase = createClient();
  const auth = await supabase.auth.getUser();
  if (auth.error) {
    return redirect("/sign-in");
  }

  // Make sure the note is valid
  const checkNote = createNoteSchema.safeParse(note);
  if (!checkNote.success) {
    return { ok: false, reason: "Invalid note" };
  }

  // Push the note to the database, selecting its fields.
  const { data, error } = await supabase
    .from("notes")
    .insert([
      {
        title: checkNote.data.title,
        content: checkNote.data.content,
        user_id: auth.data.user.id,
      },
    ])
    .select()
    .single();

  // Error out if we failed for whatever reason
  if (error) {
    console.error(error);
    return { ok: false, reason: "Failed to create note" };
  }

  // Check that we have an ID
  const { id, title, content } = data;
  if (!id) {
    return { ok: false, reason: "Failed to parse note" };
  }

  return { ok: true, id, title, content };
};
```

I'm going to leave the server action alone for unit testing purposes, because
this starts to cross the client-server boundary. That sounds more suited to an
end-to-end or integration test. That just leaves us with the page–maybe we can
test that?

### Package Setup

Let's grab some packages. I'm using vitest because it has better support for
typescript than some other test runners, and because it has a watch mode that
I'm more fond of:

```
npm install -D vitest @vitejs/plugin-react jsdom @testing-library/react @faker-js/faker vite-tsconfig-paths
```

Now I'll write a `vitest.config.mts`, using the vite react plugin and the
tsconfig paths:

```ts
import { defineConfig } from "vitest/config";
import react from "@vitejs/plugin-react";
import tsconfigPaths from "vite-tsconfig-paths";

export default defineConfig({
  plugins: [tsconfigPaths(), react()],
  test: {
    environment: "jsdom",
  },
});
```

And add a unit test script to my `package.json`:

```ts
{
  "scripts": {
    "test:unit": "vitest"
  }
}
```

Now, we should be good to run `npm run test:unit` and start writing some tests.

### Writing a Unit Test

There's a bit of boilerplate to each test, so I'll show that here first:

`app/notes/create/__tests__/page.test.tsx`

```tsx
import { describe, expect, it, vi } from "vitest";
import { render, screen, fireEvent, cleanup } from "@testing-library/react";
import Page from "../page";

// Create a group of tests using this file's filename.
describe(module.id, () => {
  it("should render properly", () => {
    // Render the page to the JSDom
    render(<Page />);

    // Throw away this render so that other tests have a clean slate
    cleanup();
  });
});
```

Now, if this test passes, I know that it's rendering without throwing any
errors. However, that's not quite the case because I used a `useRouter` call
which depends on context, and this component is being used in isolation.

To fix this, I'll need to do some mocking. I think mocking is a good tool to
have in your toolbox, but one you should be careful of. It's easy to overuse
mocks and end up in a state where you're not testing anything at all!

That aside, to keep our test isolated let's use the mock sparingly:

`app/notes/create/__tests__/page.test.tsx`

```ts
import { describe, expect, it, vi } from "vitest";
import { render, screen, fireEvent, cleanup } from "@testing-library/react";
import Page from "../page";

// Mock the next/navigation module
vi.mock("next/navigation", async () => {
  // Import the actual module
  const actual = await vi.importActual("next/navigation");
  return {
    ...actual,
    // Replace the router with mocked calls
    useRouter: vi.fn(() => ({
      push: vi.fn(),
      replace: vi.fn(),
    })),
  };
});

describe(module.id, () => {
  it("should render properly", () => {
    render(<Page />);

    cleanup();
  });
});
```

We should be rendering fine now. But sometimes we want to know more than errors
being thrown. Let's make sure that the important elements are there:

`app/notes/create/__tests__/page.test.tsx`

```tsx
import { describe, expect, it, vi } from "vitest";
import { render, screen, fireEvent, cleanup } from "@testing-library/react";
import Page from "../page";

vi.mock("next/navigation", async () => {
  const actual = await vi.importActual("next/navigation");
  return {
    ...actual,
    useRouter: vi.fn(() => ({
      push: vi.fn(),
      replace: vi.fn(),
    })),
  };
});

describe(module.id, () => {
  it("should render properly", () => {
    render(<Page />);

    // Get these elements based on the data-testid attribute
    const titleLabel = screen.getByTestId("title-label");
    const titleInput = screen.getByTestId("title-input");
    const contentLabel = screen.getByTestId("content-label");
    const contentInput = screen.getByTestId("content-input");
    const submitButton = screen.getByTestId("submit-button");

    // Make sure that each of them exists
    expect(titleLabel).toBeTruthy();
    expect(titleInput).toBeTruthy();
    expect(contentLabel).toBeTruthy();
    expect(contentInput).toBeTruthy();
    expect(submitButton).toBeTruthy();

    cleanup();
  });
});
```

Now I should make sure that we're enforcing some of the restrictions clientside
so that regular users have a good experience:

`app/notes/create/__tests__/page.test.tsx`

```tsx
import { describe, expect, it, vi } from "vitest";
import { render, screen, fireEvent, cleanup } from "@testing-library/react";
import Page from "../page";

vi.mock("next/navigation", async () => {
  const actual = await vi.importActual("next/navigation");
  return {
    ...actual,
    useRouter: vi.fn(() => ({
      push: vi.fn(),
      replace: vi.fn(),
    })),
  };
});

describe(module.id, () => {
  it("should render properly", () => {
    render(<Page />);

    const titleLabel = screen.getByTestId("title-label");
    const titleInput = screen.getByTestId("title-input");
    const contentLabel = screen.getByTestId("content-label");
    const contentInput = screen.getByTestId("content-input");
    const submitButton = screen.getByTestId("submit-button");

    expect(titleLabel).toBeTruthy();
    expect(titleInput).toBeTruthy();
    expect(contentLabel).toBeTruthy();
    expect(contentInput).toBeTruthy();
    expect(submitButton).toBeTruthy();

    cleanup();
  });

  // Create another test to test short titles within the same
  // filename based group.
  it("should not accept titles shorter than 2 characters", () => {
    render(<Page />);

    const titleInput = screen.getByTestId("title-input");
    const submitButton = screen.getByTestId("submit-button");

    expect(titleInput).toBeTruthy();
    expect(submitButton).toBeTruthy();

    // Type an "a" into the title input
    fireEvent.change(titleInput, { target: { value: "a" } });

    // Click the submit button
    fireEvent.click(submitButton);

    // Look for the title message now that we've made it appear
    const message = await screen.findByTestId("title-message");
    expect(message).toBeTruthy();
    expect(message.textContent).toBe(
      "String must contain at least 2 character(s)"
    );

    cleanup();
  });
});
```

#### Aside: Setup Functions

The above starts to get repetitious. One option to consolidate that code
together is a setup function, like this:

`app/notes/create/__tests__/page.test.tsx`

```ts
import { describe, expect, it, vi } from "vitest";
import { render, screen, fireEvent, cleanup } from "@testing-library/react";
import Page from "../page";

describe(module.id, () => {
  const setup = () => {
    vi.mock("next/navigation", async () => {
      const actual = await vi.importActual("next/navigation");
      return {
        ...actual,
        useRouter: vi.fn(() => ({
          push: vi.fn(),
          replace: vi.fn(),
        })),
      };
    });

    const utils = render(<Page />);

    const titleLabel = screen.getByTestId("title-label");
    const titleInput = screen.getByTestId("title-input");
    const contentLabel = screen.getByTestId("content-label");
    const contentInput = screen.getByTestId("content-input");
    const submitButton = screen.getByTestId("submit-button");

    return {
      titleLabel,
      titleInput,
      contentLabel,
      contentInput,
      submitButton,
      utils,
    };
  };

  it("Should Render", () => {
    const { titleLabel, titleInput, contentLabel, contentInput, submitButton } =
      setup();

    expect(titleLabel).toBeTruthy();
    expect(titleInput).toBeTruthy();
    expect(contentLabel).toBeTruthy();
    expect(contentInput).toBeTruthy();
    expect(submitButton).toBeTruthy();

    cleanup();
  });

  it("Should not accept titles less than 2 characters", async () => {
    const { titleInput, submitButton } = setup();

    expect(titleInput).toBeTruthy();
    expect(submitButton).toBeTruthy();

    fireEvent.change(titleInput, { target: { value: "a" } });
    fireEvent.click(submitButton);

    const message = await screen.findByTestId("title-message");
    expect(message).toBeTruthy();
    expect(message.textContent).toBe(
      "String must contain at least 2 character(s)"
    );

    cleanup();
  });
});
```

I find these are a good default for setting up state, mocks, and any
dependencies that my test needs to have in place.

#### Aside: Wrapper Functions

An other option is with a wrapper function that does the setup and passes the
context down:

`app/notes/create/__tests__/page.test.tsx`

```tsx
import { describe, expect, it, vi, type TestFunction } from "vitest";
import { render, screen, fireEvent, cleanup } from "@testing-library/react";
import Page from "../page";

describe(module.id, () => {
  const runTest = (
    testFn: (elements: {
      titleLabel: HTMLElement;
      titleInput: HTMLElement;
      contentLabel: HTMLElement;
      contentInput: HTMLElement;
      submitButton: HTMLElement;
    }) => Promise<void> | void
  ): TestFunction<object> => {
    return async () => {
      vi.mock("next/navigation", async () => {
        const actual = await vi.importActual("next/navigation");
        return {
          ...actual,
          useRouter: vi.fn(() => ({
            push: vi.fn(),
            replace: vi.fn(),
          })),
        };
      });

      const utils = render(<Page />);

      const titleLabel = screen.getByTestId("title-label");
      const titleInput = screen.getByTestId("title-input");
      const contentLabel = screen.getByTestId("content-label");
      const contentInput = screen.getByTestId("content-input");
      const submitButton = screen.getByTestId("submit-button");

      await testFn({
        ...utils,
        titleLabel,
        titleInput,
        contentLabel,
        contentInput,
        submitButton,
      });

      cleanup();
    };
  };

  it(
    "Should Render",
    runTest(
      ({
        titleLabel,
        titleInput,
        contentLabel,
        contentInput,
        submitButton,
      }) => {
        expect(titleLabel).toBeTruthy();
        expect(titleInput).toBeTruthy();
        expect(contentLabel).toBeTruthy();
        expect(contentInput).toBeTruthy();
        expect(submitButton).toBeTruthy();
      }
    )
  );

  it(
    "Should not accept titles less than 2 characters",
    runTest(async ({ titleInput, submitButton }) => {
      expect(titleInput).toBeTruthy();
      expect(submitButton).toBeTruthy();

      fireEvent.change(titleInput, { target: { value: "a" } });
      fireEvent.click(submitButton);

      const message = await screen.findByTestId("title-message");
      expect(message).toBeTruthy();
      expect(message.textContent).toBe(
        "String must contain at least 2 character(s)"
      );
    })
  );
});
```

I think these have an advantage over setup functions since they can do things
like clean up after themselves, but they have added complexity in that they tend
to be longer and need explicit types which makes them harder to modify once
they're set up–look at all of the weird things I need to do, like returning a
function and the other async shenanigans.

I recommend these for tasks that have cleanup or otherwise should be very clear
that an explicit scope is happening.

## Automated Tests

Tests are well and good if you remember to run them (or if you add something
like `husky` and make them run before committing code), but ideally we'd have a
guarantee that we run them before merging to production. This is the heart of
Continuous Integration (CI), which I think is the harder part of CI/CD (because
you have to make sure you have enough test coverage to feel safe enough running
whatever your CD looks like).

I use GitHub by default, and GitHub gives me an integrated CI runner in GitHub
Actions. Here's a simple workflow for running unit tests:

`.github/workflows/unit-tests.yml`

```yaml
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  unit-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Setup Node.js
        uses: actions/setup-node@v2
        with:
          node-version: 20
          cache: "npm"

      - name: Install dependencies
        run: npm ci

      - name: Run unit tests
        run: npm run test:unit
```

If you're worried about version incompatibilities (like if you're writing a
library), you can also use a matrix strategy:

`.github/workflows/unit-tests.yml`

```yaml
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [16.X, 18.X, 20.X, 22.X]

    steps:
      - uses: actions/checkout@v3
      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v2
        with:
          node-version: ${{ matrix.node-version }}
          cache: "npm"

      - name: Install dependencies
        run: npm ci

      - name: Run unit tests
        run: npm run test:unit
```

For most purposes, I find that this is overkill. You should match whatever you
plan to do in production, and branch out when you need it. Otherwise it's a lot
of wasted compute time.

## Other Tooling Worth Considering

### Build Check

Generally, it's a good idea to make sure that the application will build at all
before running tests, and it's a good sanity check to make sure that you'll be
able to deploy fine.

These can help you catch syntax and other issues before they cause issues in
further steps.

### Linters

Linters are awesome, since they can help you catch potential bugs and help you
write more conventional code. There are a wide variety of them, along with a
good collection of plugins for JavaScript. There are a few that I'm aware of for
Python.

I'd do this one after testing, perhaps in its own optional job, since at that
point it's up to me if I want to do a deploy with working but stylistically
incorrect code.

### Container Build

Another neat piece of a CI pipeline that starts to move towards CD is building a
container and publishing it to some container registry. I have it set up so that
when I merge things to `main` in my tag tracker project, I run a build on fly.io
and have that container then published.
