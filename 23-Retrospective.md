# Lecture 23 - Retrospective

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [Lecture 23 - Retrospective](#lecture-23---retrospective)
  - [Links](#links)
  - [News](#news)
  - [Retrospective](#retrospective)
    - [What went well?](#what-went-well)
    - [What didn't go well?](#what-didnt-go-well)
    - [What did you learn?](#what-did-you-learn)
    - [If you could change one thing that you did this semester, what would it be?](#if-you-could-change-one-thing-that-you-did-this-semester-what-would-it-be)
  - [Hunter's Musings](#hunters-musings)
    - [AI and Development](#ai-and-development)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Links

- [Feedback Form](https://docs.google.com/forms/d/e/1FAIpQLSdcu-u0LD5kB9rhOcA7E1ZCw6w05RlejzrFrRALEz7krkLjVQ/viewform?usp=sf_link)
- [Q&A Form](https://docs.google.com/forms/d/e/1FAIpQLSd4c3JqKFSybays7xUNk3EeiUaDak7XvRqRyosng0ATCZf2bQ/viewform?usp=sf_link)
- [Office Hours](https://calendly.com/hhenrichsen)

## News

## Retrospective

Let's talk about what has gone well and what has not gone well so far this
semester.

Our goal from this is to learn from each other, not to place the blame on
people:

> **Regardless of what we discover, we understand and truly believe that
> everyone did the best job they could, given what they knew at the time, their
> skills and abilities, the resources available, and the situation at hand**

– Norm Kerth, Project Retrospectives: A Handbook for Team Review, quoted in
[the Retrospective Wiki](https://retrospectivewiki.org/index.php?title=The_Prime_Directive).

### What went well?

### What didn't go well?

### What did you learn?

### If you could change one thing that you did this semester, what would it be?

## Hunter's Musings

### AI and Development

With the recent news about Shopify
[requiring AI usage](https://www.forbes.com/sites/douglaslaney/2025/04/09/selling-ai-strategy-to-employees-shopify-ceos-manifesto/),
[Bill Gates and Sam Altman](https://www.windowscentral.com/software-apps/sam-altman-ai-will-make-coders-10x-more-productive-not-replace-them)
saying that AI will make developers 10x more productive, and
[some vibe coders](https://nmn.gl/blog/vibe-coding-fantasy) finding out that
things don't work as well in production, I've had a couple questions about what
the future of development looks like.

I think for smaller codebases, especially that are built around pretty
boilerplate things (like API endpoints, database schemas, and other "basic"
features of an app) can be built super quickly. Especially if you can clearly
write the requirements, I think AIs will be able to build the more trivial,
boring parts of apps.

I heard an interesting idea recently about being able to work with an LLM to
write a spec for changes, then have an AI agent apply it. I think that's
probably the direction that things will go, with more focus around communicating
requirements clearly.

I think there are a couple things in the way of replacing my current job,
though:

1. Context windows aren't large enough; I can keep an abstract version of a
   codebase in my head, but the LLM doesn't have the space in its "head" to
   store the full codebase or create abstractions to reason about it.
2. Architecture; many architectures are based around the problem that they're
   solving, and most of the AI responses that I've had as of recently have still
   been pretty average.
3. Novel solutions; AI struggle with unusual problems, especially those that
   haven't been spoken about publicly.
4. Determinism; most current AI tools have a reputation for fixing one thing and
   introducing additional issues as they do it.

Right now, this means a lot of reading code and a lot of review. Unfortunately
for me, about 70% of the part of my job where I work with the code is reading
code. I can ask the codebase via AI tools, but getting the right things into
context is required, and most of the time if I know something belongs in
context, I'm 80~90% of the way there to understanding it.
