# Lecture 03 - Q&A Day 1

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [Lecture 03 - Q\&A Day 1](#lecture-03---qa-day-1)
  - [Pre-Lecture](#pre-lecture)
    - [Links](#links)
    - [Events and Announcements](#events-and-announcements)
    - [News](#news)
    - [Follow-Up from Last Time](#follow-up-from-last-time)
  - [Priorities of a Startup](#priorities-of-a-startup)
  - [Serverside Rendering](#serverside-rendering)
    - [Why Do Serverside Rendering?](#why-do-serverside-rendering)
    - [How Does Serverside Rendering Work?](#how-does-serverside-rendering-work)
  - [Hosting](#hosting)
    - [Where Should I Host?](#where-should-i-host)
    - [About Vercel](#about-vercel)
    - [About Lambdas](#about-lambdas)
    - [About AWS / GCP / Azure](#about-aws--gcp--azure)
    - [About Containerization](#about-containerization)
  - [What Are Good Resources for Learning Web Design?](#what-are-good-resources-for-learning-web-design)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Pre-Lecture

### Links

- [Feedback Form](https://docs.google.com/forms/d/e/1FAIpQLSdcu-u0LD5kB9rhOcA7E1ZCw6w05RlejzrFrRALEz7krkLjVQ/viewform?usp=sf_link)
- [Q&A Form](https://docs.google.com/forms/d/e/1FAIpQLSd4c3JqKFSybays7xUNk3EeiUaDak7XvRqRyosng0ATCZf2bQ/viewform?usp=sf_link)
- [Office Hours](https://calendly.com/hhenrichsen)

### Events and Announcements

_I mostly know about Lucid events, but feel free to slack me a day or two before
class and I can get things added here._

### News

_In the future, this will be a segment where we talk about things that are going
on in the tech space that are worth knowing about. Please slack a link to a news
article about it after you get called on._

### Follow-Up from Last Time

## Priorities of a Startup

- [Stop Designing Web Applications for Millions](https://www.darrenhorrocks.co.uk/stop-designing-web-applications-for-millions)

## Serverside Rendering

### Why Do Serverside Rendering?

For any pages you want to be indexed by search engines, like your landing page
or any marketing pages, you should use serverside or static rendering if
possible.

Most search engines use a rendering budget where they give your pages a limited
amount of time to render before they get marked to be done later. Some do this
as an amount of time by domain, some do this as time per page. Static pages are
ideal for these since the content is right there for the indexer to consume. As
well, if you want any opengraph metadata (like previews in Slack, Twitter,
Discord, etc.), that will need to be static as well.

### How Does Serverside Rendering Work?

![[Pasted image 20240923211705.png]]

## Hosting

A lot of you asked about hosting, and so this is the majority of what we talked
about.

### Where Should I Host?

Here are some of the questions I think about when I'm asked this:

- **Do you have users yet?** If not, host on your computer.
- **Do you need a backend yet?** If not, host on a static service like GitHub
  Pages, CloudFlare Pages, Netlify, or object storage (like S3).
- **Do you have enough demand to consistently be running servers or exceeding
  free plans?** If not, you might choose a host like Vercel. Just be careful of
  lock-in and such – to that end, I recommend putting together a containerized
  version of your app.
- If you've made it here, I recommend something like fly.io or a small VPS (on
  Vultr or Hetzner) to get started. I think AWS or Kubernetes have way more
  complexity than are worth the time on a small team, but containerization and
  knowing how your underlying technologies work are a huge benefit.
- Finally, if you don't like the advice in the previous note, feel free to move
  towards something like finding server space in a data center or using AWS.
  I've found these are more logistics, setup, and continued configuration than
  is worth it for me. But you might find value there depending on your project.

Ultimately, it depends a lot on your project, and you can spend a long time
chasing something that works well and is the most cost effective. I've found
that gets in the way of actually building what I want to build, though, so when
I find something that works okay and doesn't break my budget, I don't tend to
move much from there.

I've recently been pretty fond of fly.io and its ability to scale to zero. That
keeps costs super low, and they give you a lot of tooling along with the cost
there (live logs, metrics, secret management, easy CI/CD, deployment on a
consistent subdomain, etc.)

### About Vercel

I think Vercel's free plan is great, and even their paid plan is reasonable. But
their costs are a lot higher than the lambdas and object storage they use to
host them, and they don't give out the tools to build your own app that way
(which makes sense, but goes against my general open source philosophy). There
is some amount of third party work like SST / OpenNext, but those are community
maintained and have some odd edge cases.

### About Lambdas

I think lambdas are awesome, especially when you're small. However, as you
scale, they tend to get a little bit trickier. I want to try building an app
using only "serverless" technology, but I don't anticipate scaling an app like
that.

Here are some different things people have found about serverless:

- https://shayy.org/posts/a-few-servers
- https://world.hey.com/dhh/don-t-be-fooled-by-serverless-776cd730
- There's an article of which only archives exist called "Scaling up the Prime
  Video audiovideo monitoring service and reducing costs by 90%" which is worth
  a look as well.

Generally, they're really good for short, one-off tasks that run infrequently.
If you're running them constantly, you're paying for a lot of compute overhead
(and time overhead, since they are designed to stop and start and have certain
assumptions baked into them). There are good use cases for them, and bad ones.

### About AWS / GCP / Azure

I think cloud providers like these are cool, especially since they tend to be
the lowest cost options available when looking at only cost vs compute.

However, in order to deploy things "correctly" on a cloud provider, it gets
complex. What I can do on Vercel + Supabase free plans, I need the following to
recreate the same on AWS:

- An EC2 instance for my database (~$6/mo for a develop tier on RDS, the lowest)
- Some EBS allocated to the database (varies by size and backup frequency)
- An EC2 instance / an ECS instance / an EKS instance / lots of lambdas + a
  router for my backend (free + API and log usage for lambdas, ~$5/mo for the
  EC2, similar or higher costs for everything else)
- An S3 bucket for any static parts of my site like compiled JS, CSS, and HTML
  (varies based on size)

### About Containerization

I think containers are good for your app. I think they boost developer
experience, and make production deployment consistent if they're built right.
They're also a stepping stone towards Kubernetes without all of the same
complexity, and allow you to deploy on container-based compute services (like
ECS on AWS; equivalents on GCP and Azure exist).

## What Are Good Resources for Learning Web Design?

Overall, my biggest advice here is to look at sites that you think are well
designed and try to reverse-engineer why they are that way. What do they do with
their spacing? Their colors? Why is that interaction pattern successful? Why did
they build that component that way?

Being curious about why things work and then digging into them is how I learned
a lot of what I know. That said, I've also been looking at design resources for
awhile so I've absorbed some of that in ways that are hard to express verbally
or prosaically. Here are some of those:

Here are some useful design / development resources:

- [Appendix A of these notes](./Appendix/A - Design Notes.md), which have some
  other resources.
- [Designary Archive](https://blog.designary.com/archive), which covers some UX
  fundamentals which I find useful for both working with and without designers.
- [CSS Tricks](https://css-tricks.com/), especially FlexBox, but they have many
  other useful resources.
- [The MDN](https://developer.mozilla.org/en-US/) has a lot of web fundamentals
  that are worth reviewing if you've not already done so.
