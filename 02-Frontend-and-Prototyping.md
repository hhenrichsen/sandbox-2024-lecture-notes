# Lecture 02 - Frontend and Prototyping

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

**Table of Contents**

- [Lecture 02 - Frontend and Prototyping](#lecture-02---frontend-and-prototyping)
  - [Pre-Lecture](#pre-lecture)
    - [Links](#links)
    - [Events and Announcements](#events-and-announcements)
    - [News](#news)
    - [Follow-Up from Last Time](#follow-up-from-last-time)
      - [(Advice) Validation and Curiosity](#advice-validation-and-curiosity)
      - [(Advice) What Database Would You Recommend for Analytics and Data Analysis?](#advice-what-database-would-you-recommend-for-analytics-and-data-analysis)
      - [(Advice) What Tech Stack Do You Recommend for Fast Iteration?](#advice-what-tech-stack-do-you-recommend-for-fast-iteration)
  - [Prototyping](#prototyping)
    - [(Advice) Learn from Prior Art](#advice-learn-from-prior-art)
    - [(Advice) Keep Things Rough](#advice-keep-things-rough)
    - [(Advice) Have Multiple Versions (at Least 3)](#advice-have-multiple-versions-at-least-3)
    - [(Advice) Get Things in Your (and Users') Hands](#advice-get-things-in-your-and-users-hands)
    - [(Demo) Let's Design a Page](#demo-lets-design-a-page)
  - [Frontend](#frontend)
    - [(Lecture) Interactive Applications Are Built on the DOM and JavaScript](#lecture-interactive-applications-are-built-on-the-dom-and-javascript)
      - […and Sometimes More](#and-sometimes-more)
      - [Props In, Listeners Out](#props-in-listeners-out)
    - [(Lecture) There Are Lots of Devices](#lecture-there-are-lots-of-devices)
    - [(Advice) Pick a Library and Stick to It](#advice-pick-a-library-and-stick-to-it)
      - […But Pick One That Works For You](#but-pick-one-that-works-for-you)
  - [(Demo) Bootstrapping a Next.js Project](#demo-bootstrapping-a-nextjs-project)
  - [For Next Time](#for-next-time)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Pre-Lecture

### Links

- [Feedback Form](https://docs.google.com/forms/d/e/1FAIpQLSdcu-u0LD5kB9rhOcA7E1ZCw6w05RlejzrFrRALEz7krkLjVQ/viewform?usp=sf_link)
- [Q&A Form](https://docs.google.com/forms/d/e/1FAIpQLSd4c3JqKFSybays7xUNk3EeiUaDak7XvRqRyosng0ATCZf2bQ/viewform?usp=sf_link)
- [Office Hours](https://calendly.com/hhenrichsen)

### Events and Announcements

_I mostly know about Lucid events, but feel free to slack me a day or two before
class and I can get things added here._

- Lucid Tech Talk / ACM Opening Social - next Tuesday (September 17th) in TMCB
  1170! Come hear from me and Alex Blackburn about how and why we built a
  developer platform at Lucid if you want to hear me talk twice in a week.

### News

_In the future, this will be a segment where we talk about things that are going
on in the tech space that are worth knowing about. Please slack a link to a news
article about it after you get called on._

### Follow-Up from Last Time

#### (Advice) Validation and Curiosity

I was talking to Sawyer and Tate after class last time, and I think Sawyer
expressed something way better than I did while I was talking about things to be
working on if you're still trying to validate your idea.

Be curious.

Especially when you're trying to learn how to build something, or even explore
how you might build something in the future, dig into how that works and build
up your foundation. When you're in a learning mode, do your best to cut out as
much of extra scope as possible, and focus on figuring out that thing that
you're learning.

I like to take a 50% rule (one different from the one we talked about last time)
when learning new things; the sum of scope, features, and technologies should be
no more than 50% new if I want to work productively.

There are some app ideas you could throw at me and I'd have them built super
quickly because I have the foundation for them, and I've already solved many of
the inherent problems. You'll struggle to build that foundation as much when
you're trying to ship something fast, so I find it's worthwhile to do
smaller-scope learning experiments before trying to add that scope to something
even larger, that way I manage the other factors in my 50% rule.

#### (Advice) What Database Would You Recommend for Analytics and Data Analysis?

This is one of the things I'd get off the shelf, something like Hotjar or
PostHog. I think it's easy to run into issues where the analytics database
becomes super heavy and needs to be split out.

If you're looking for something to do on your own, you can probably get away
with a normal database, like Postgres, MySQL, or MongoDB. The latter is the most
flexible for things like analytics because you can put whatever you want in
there. Postgres and MySQL generally require more manual management for tables
and migrations, but are better optimized for querying structured data.

Also, a lot of people use Snowflake and other similar providers in the industry,
so they can be worth looking into. But I wouldn't over-optimize on this to start
with.

#### (Advice) What Tech Stack Do You Recommend for Fast Iteration?

I'll disclaimer this again by saying that you should learn as much as you can
from low-cost solutions. If a phone call or conversation will work, learn from
that. If paper prototypes work, learn from those. If Figma prototypes work,
learn from those. You shouldn't need to code up a full prototype just to learn
if it's a good experience for whoever you're going to be selling it to.

Also, a big plug for actual pen and paper (and some markers if you're feeling
ambitious). Learning a design tool is valuable, but you can often get something
sketched out quicker and decide if it's worth pursuing on a piece of paper than
in a design tool where it's a lot easier to fall into pixel pushing and playing
with colors and whatnot.

That said, I'm personally a fan of Ktor and Postgres, which keeps me in the same
codebase for frontend + backend, supports a basic version of HMR, and is fully
SSR. It has a bit of a learning curve, and there's not a lot of magic there so
anything fancy you have to build yourself, but I like having that control and
being able to build what I like, as well as to be able to take advantage of the
Java library ecosystem.

Postgres has built-in support for some things I frequently like to use, like
PostGIS and UUIDs. I'll also normally host it on some VPS until it outgrows the
one I share between a few services, at which point I'll send it to its own
server. I'm planning on tinkering with Kubernetes soon as well, which might
replace things on their own server.

If you aren't super opinionated about what your backend should look like, I'd
grab one of the full stack JS frameworks like Svelte or Next.js. They're
popular, and have lots of resources to get you started, they have polished
developer experiences (especially with things like hot module reloading), and
have a lot of features built in which can help to accelerate you without needing
to build those things on your own. You do give up some control over core
features and how your organize your codebase in turn, but that can be worthwhile
when you're first building something since it gives you structure to follow.

Regardless of what I use, I tend to throw in UnoCSS (a tailwind alternative) so
that I can prototype things quickly, although I like to move things away from
that as the designs get more solid.

## Prototyping

### (Advice) Learn from Prior Art

There are many things that are solved problems in terms of design, and when
working with a designer (or even when working without one!), knowing what these
are, how they work, and why they are the way they are is incredibly valuable
knowledge.

I encourage you to be curious and attentive here; there's a lot to learn from
products that you and thousands or millions of other people use day to day. Here
are some spots you can check out:

- [Interface Index](https://interface-index.com/) - a collection of components
  and how they work in the wild
- [Collect UI](https://collectui.com/designs) - a collection of complete UIs,
  categorized by purpose

### (Advice) Keep Things Rough

Like I mentioned before, it's really easy to get into pixel pushing and making
tiny tweaks. You'll generally be able to tell if something will work or not
before you need to decide if 4px or 8px of spacing is enough.

### (Advice) Have Multiple Versions (at Least 3)

Something I've found is that especially for early explorations, it's really easy
to get pigeonholed into the one design that you really like without critically
thinking about what options there are.

Concept Artists do something called thumbnail sketches, where they start out
exploring an idea by drawing really small versions of the idea and figuring out
what works and doesn't work. Then they refine a couple of those into more
polished sketches, and eventually will pick the most promising one to be
actually turned into a finished illustration.

Fiction Writers also have something similar; many stories start as vignettes,
single scenes pulled out of any other context. They then will be written into
short stories, to see if the characters, setting, and plot have promise, before
finally they turn into more fleshed out stories.

I think this is a useful practice for engineers (or engineers acting as
designers, sometimes), too. Think through multiple potential implementations,
and eliminate the ones that won't work or are too complex. Same goes for
potential designs; don't force yourself into a single design or implementation
because it's the first one you thought of.

One other thing I think is useful to steal from other industries is warm ups.
Artists do all sorts of warm up sketches. Writers are frequently advised to
write at least one short story a day. What did you do to warm up your problem
solving and engineering skills today?

### (Advice) Get Things in Your (and Users') Hands

I mentioned this last week, but I think this post on
[The 50 Percent Rule of Building Software](https://medium.com/@mduffield_35249/the-50-percent-rule-eab97c624582)
is super true, and it sounds like you've been hearing it in your other classes
too. You'll build a much better product building one alongside people who use it
frequently than you will building a likely overdesigned and overengineered
product that nobody could use if they wanted.

### (Demo) Let's Design a Page

Let's design a page on a site. What should we build?

## Frontend

I'm going to touch on some fundamentals here since, at least in my school
experience, I didn't build a lot of full apps and therefore took some of my own
development to learn how to do some of what I needed for my projects (and at
work).

### (Lecture) Interactive Applications Are Built on the DOM and JavaScript

The DOM is core to how the modern web works. It's part of what enabled all of
the frameworks we see nowadays to pop up and thrive, and at it's core it's just
a set of consistent APIs to interact with the HTML on a webpage.

Before the DOM, there was jQuery. Or perhaps, a better way of saying it is that
jQuery _was_ the DOM before there was an agreed upon standard. My observation is
that many of the shared APIs are modeled after the APIs that jQuery provided.

#### …and Sometimes More

The modern web has enabled browsers to be a lot more powerful, starting off with
simpler elements like the HTML Canvas and moving into more powerful ones with
WebGL and WebGPU. More interactive and custom apps sometimes need that power, so
being aware of at least what options are available can be super useful.

#### Props In, Listeners Out

With HTML, the general pattern for communication is props for inputs, and
listeners for outputs. For example, here I construct a form using props like
`for` and `type` and `id`:

```html
<form>
  <label for="name"> Name </label>
  <input id="name" name="name" type="text" />
  <button id="submit" type="submit">Submit</button>
</form>
```

When I want information _out_ of the component, I need to listen to an event, or
otherwise go fetch it myself, for example here I use a `click` listener:

```js
document.getElementById("submit").addEventListener("click", (event) => {
  event.preventDefault(); // don't reload the page
  console.log(document.getElementById("name").value);
});
```

### (Lecture) There Are Lots of Devices

Some users will be on phones, some will be on tablets, some will be on
touch-enabled monitors, some will have what you've built split-screened with
something else, some will be on 31:9 aspect ratios, and some will be on 1:1
aspect ratios. Ultimately, figuring out a solution that works on most devices is
worthwhile.

The main way of doing this is designing your site flexibly, and using the
devtools to make sure it works at many screen sizes.

### (Advice) Pick a Library and Stick to It

_…at least long enough to learn it._

Learning one library is generally good enough to get you started on another one
if you need to swap. It won't be deep knowledge, but it'll be enough where you
can spend 20~30 minutes reading the intro to any other library's docs and be
able to use that, at least. Because all of the libraries are built on top of the
DOM, they all share some amount of structure.

Anything groundbreaking generally gets reimplemented into other libraries where
possible, so we have some amount of convergent evolution.

#### …But Pick One That Works For You

I've built sites without using a library. I've built them on Vue and Nuxt. I've
built them on Angular. I've built them on Kotlin. A toy color picker doesn't
need the overhead of SSR and can be entirely static.

Pick something that meets the needs of what you're doing. Some libraries have
different struggles at scale, but when you're validating, picking one you can
work quickly in much more beneficial than trying to shoehorn yourself into one
that's popular so you can hire an engineer, and then crashing and burning after
a few months.

There are few decisions that are impossible to undo or rebuild in another
language. Some of them are more expensive than others.

## (Demo) Bootstrapping a Next.js Project

To start off, I'm going to run the `create-next-app` package:

```
npx create-next-app@latest
```

I'm using the `src` folder because that's similar to how I use things, and using
TypeScript because I think it's a helpful tool to keep me from writing bugs.

I'm also going to grab [the shadcn component library](https://ui.shadcn.com/):

```
npx shadcn init
```

And lastly, I'm going to grab
[Zustand](https://zustand.docs.pmnd.rs/getting-started/introduction) (although
[Jotai](https://jotai.org/) or [Redux](https://redux.js.org/) are also very cool
and commonly used for state management), although we might not use it in class:

```
npm install zustand
```

## For Next Time

- [Submit questions for Tanner Linsley](https://docs.google.com/forms/d/e/1FAIpQLSctUSWW-HPG36MuwysCZWIrE25KfuMXqRN4exYZi5vqGOEGAg/viewform?usp=sf_link)
