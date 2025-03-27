# Lecture 21 - Q&A Day 4

<!-- START doctoc generated TOC please keep comment here to allow auto update -->

- [Lecture 21 - Q&A Day 4](#lecture-21---qa-day-4)
  - [Links](#links)
  - [Events](#events)
  - [News](#news)
  - [On Monitoring](#on-monitoring)
  - [On Rust](#on-rust)
  - [On Freelance Work](#on-freelance-work)
    - [Have You Ever Done Freelance Work?](#have-you-ever-done-freelance-work)
    - [Would You Recommend Freelance Work?](#would-you-recommend-freelance-work)
    - [How Would You Go about Freelance Work?](#how-would-you-go-about-freelance-work)
  - [On Migrations](#on-migrations)
    - [Why Migrations](#why-migrations)
    - [Adding a Local Database](#adding-a-local-database)
    - [Creating and Using Migrations](#creating-and-using-migrations)
    - [Generating Types](#generating-types)
    - [Running Migrations](#running-migrations)
  - [On Environments and New Hires](#on-environments-and-new-hires)
  - [System Design Problem](#system-design-problem)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Links

- [Feedback Form](https://docs.google.com/forms/d/e/1FAIpQLSdcu-u0LD5kB9rhOcA7E1ZCw6w05RlejzrFrRALEz7krkLjVQ/viewform?usp=sf_link)
- [Q&A Form](https://docs.google.com/forms/d/e/1FAIpQLSd4c3JqKFSybays7xUNk3EeiUaDak7XvRqRyosng0ATCZf2bQ/viewform?usp=sf_link)
- [Office Hours](https://calendly.com/hhenrichsen)

## Events

## News

## On Monitoring

I just wanted to check back in on something that I mentioned in passing and in
my notes a couple weeks ago, but didn't end up talking much about.

[Prometheus](https://prometheus.io/docs/introduction/overview/),
[Grafana](https://grafana.com/oss/grafana/), and similar software can be really
useful for building a monitoring setup. Getting started is pretty simple; you
set up an endpoint on your production app, and check in on it every minute or
every second. Then these apps can help you look at that data over time, send
alerts, or collect logs.

It's way easier to debug something when you have the information to make a
conclusion, rather than needing to first log the information then debug things.

## On Rust

_Thoughts on Rust being the C of the next 40 years?_

I think Rust is neat, but it's a hard language to get into, especially as
projects become more and more complex. I think it's harder to make mistakes in
Rust than it is in C or C++, but in turn you have to consider those mistakes
while you're writing code. For performance- and memory-critical code, this is
ideal. You don't want mistakes.

For products, sometimes you need to move quickly though, and the developer
experience of writing new features is important. If you fully grok Rust, you can
write products and features without being too hindered. From what I've seen and
experienced, not everyone fully groks Rust, though.

I think that editions are a fantastic idea, and helps to address one of my big
problems with C, C++, and JavaScript. Old code has to run properly on new
versions of the language, so tech debt is permanent. Some program will exist
that has to continue existing that takes advantage of one little quirk that
wasn't intended. Editions solve this gracefully; if you want new features and
new behaviors, you have to upgrade your code beyond the old solutions. If you
can't afford that investment, your code will continue to work without breaking
things for the other users of the language.

## On Freelance Work

_Have you ever done freelance development work? If so, would you recommend it?
If so, how do you recommend going about it?_

### Have You Ever Done Freelance Work?

I've done two things that may have been considered freelance work. I've taken
commissions for Minecraft plugins, and I solved some tickets for one of my
friend's startup.

For the Minecraft plugin development, I made a very small amount of money in
exchange for a much larger amount of work than I should have put in, but I think
the experience was valuable. I took some paid feature requests for a plugin I
maintain, and built a plugin from the ground up for another person. I learned
how to work with customers and the value of showing people prototypes. I got
some good reviews out of the process and earned a little bit of spending money
while I was in school.

I got to read a lot of code, too. That helped me get a good understanding for
the types of projects I liked to work within, and the types of projects that I
hated. Over time those turned into code standards that I'd hold my own projects
to, although there were also times where I'd ignore them and come to regret them
later. One of the reasons I was so excited to work at Lucid was to hear from the
engineers that they had similar standards to the type of code that I wanted to
write, so it was much easier for me to jump in and get started.

I also learned about the number of places an arrangement like the one I was
looking into could go wrong, or otherwise take a rough turn. I definitely
wouldn't want to do this kind of stuff full time, but part of that's the younger
(and a touch more entitled) audience, and the fact that people turn up and ask
for support then vanish and leave a negative review.

For my friend's startup, I was in over my head. My self-set rate was super low
because I wasn't confident in my abilities yet, and I was kind of just handed a
repo and told to figure things out. That ended up being a great intro to Docker
and a couple other tools I still think are cool (like Grafana), but it also was
a bit more complex than I was ready to handle at that point.

I was tasked to add some filtering to one of the views on the mobile app. That
involved adding some new parameters to backend routes which I was able to do
fine, but the other founders wanted a specific interface for searching a range
of values. I got that working on android, but since I only owned a Windows
computer and an Android phone, I couldn't test it. I deployed the app from my
friend's computer, and heard back on my way up to Utah State for my Junior year
that things were broken. The other founder got back from the trip (that had been
the reason that I got the job in the first place) and was able to fix it.

I found that I didn't really like hour tracking (especially since I tended to
jump between a couple things at once, and then short-change my time) for
projects like that, and that I wasn't really ready for that type of work yet. It
did, however, show me that there was still a lot I needed to learn, and teach me
how to deal with larger projects that I wasn't involved in from the beginning.

As things ended up, the end of that Junior year was the one where I built a much
more complex infection tag site, so it also gave me a good perspective for how
far I'd come.

Given, both of these experiences were before AI was as big, so they may seem
like pretty simple in retrospect. I think a big part of how it would look
nowadays would emphasize your ability to not only do the work (as many people
can vibe-code _something_ into existence), but to demonstrate your ability to do
it well and to produce code that's reliable. A lot of it's about branding, and
"why should I pay you to do it instead of paying for Cursor myself" is part of
that branding.

### Would You Recommend Freelance Work?

I think it's a great way to get practical experience, especially in project
structures that you've never worked in before. Some projects will have you
working from scratch and leave it up to you to make decisions, but you also have
to think about how the thing keeps running after you stop working on it.

The flipside is time and marketing; if you have a surplus of time, freelance is
a good way to get experience and pay. You have to build a brand and know your
value before that works, though. Some opportunities can arise from networking,
but you and your clients need constant growth if you want to sustain yourself.

### How Would You Go about Freelance Work?

Some people who do freelance don't take pay for individual projects, but instead
take a subscription that includes maintenance and new features on projects as
well as hosting and other operations work. If I were wanting to make a living
off of freelance work, that's the structure I'd go after. I enjoy the consulting
and problem solving parts of the work more, though, so perhaps I would look more
towards consulting instead of freelancing.

## On Migrations

_Can you go over how to set up a local supabase database in a Next project and
then a database migration manager?_

### Why Migrations

I think migrations are useful for two reasons:

1. Rather than needing to maintain two sources of truth about your application
   structure, you only need to maintain one.
2. You can make incremental changes to your database structure without needing
   to take your site down for maintenance.

There are plenty of libraries that can do it for you. I keep sql files checked
in to my Java resources and have my server run those on startup, which makes it
easy for me to be sure that the db and app are working on the same schema.

Supabase will mostly manage these for you if you have it integrated into your
projected; I think it's a benefit worth using.

### Adding a Local Database

Install the Supabase CLI and Docker, then run:

```
supabase init
```

This will create a `supabase` folder in the root of your project. The main
entries of interest in there are the `supabase/migrations` which will initially
be empty, and the `supabase/seed.sql` file which will also be empty.

It doesn't really matter your tech stack at this point, although it's easier if
you're using [12factor](https://12factor.net/) design for your
[config](https://12factor.net/config). Next, start the Supabase services by
running:

```bash
supabase start
```

Most starter apps give you environment config by default, and running the above
command should give you a bunch of environment variables. Replace your
production environment variables with the ones for the local database. You
should be up and running using the values you get out of the Supabase CLI at
this point.

### Creating and Using Migrations

To start, if you haven't done any database migrations yet, you should start by
doing:

```bash
supabase db pull [name]
```

This should pull the database structure on remote into a migration file. This
can be an init file for your database migrations from this point.

If you make changes after this point, you can either diff the database or reset
it using:

```bash
supabase db reset --local # reset database to migrations
supabase db diff --local # get difference between database and migrations
```

From there, you can make a migration file by running:

```bash
supabase migration new [name]
```

This will create an empty `supabase/migrations/[date]_[name].sql` file which you
can then fill out with the changes you want to make to your database. I
recommend keeping these incremental, generally limited to one table or
modification at a time. Your initial one can be an exception to that rule,
though, depending on when you're doing the migration.

### Generating Types

I also really like generating types based on the local database. It gives me
autocomplete, and autocomplete makes me type faster.

```bash
supabase gen types --local
```

This is the command that I always forget. By the time code is pushed, your
migrations should match your generated types.

### Running Migrations

Migration changes should be backwards-compatible, at least for the next few
releases. Your backend should be working in the same schema as your database by
the time that it gets deployed with changes. With supabase, this is ideally done
by your CD step.

Supabase provides a couple template
[GitHub Actions](https://supabase.com/docs/guides/deployment/managing-environments?queryGroups=environment&environment=ci#configure-github-actions)
that should be used for your staging / production database deploys, as well as a
CI one to make sure that migration changes are reflected in the generated types
files.

## On Environments and New Hires

I've had a couple questions about how to onboard people on to a project,
especially related to secret management and production services and such. Some
of what I talked about in the last section are the first steps I would take
here, but I wanted to touch on a deeper principle.

Ideally, you shouldn't be putting things on other people's computers that you
don't want them to have forever.

To me, if I'm paying someone to work on a project, that's going to be a copy of
the project at most. I don't need them to have database keys, API access, or
even proper access to the production administrator tools. I would much rather
have someone push a change to code (and require me to approve it) than have them
working directly with my AWS infrastructure or Supabase table editor. They can
bring their own Supabase instance if they really need to, but by the time I'm
onboarding someone onto my project I should have done my best to get a
close-enough version of it working.

The easiest way to do something like this would be with containerization, where
the only thing a new contributor needs is Docker. That tends not to work ideally
in all cases, though, so normally my happy medium is containers for any services
they need to run, and then a list of tools they need to install.

The other alternative is to own the hardware that your employees will be using.
That's the most secure, and means you can use environment management tools to
make sure that they have everything they need. It takes some up-front investment
to make sure that tools like that work, though, so you'll need to decide when
that's a valuable investment.

A middle ground is to run cloud machines and have people bring their own devices
that can connect to those. That way you have the advantage of environment
management and control, but these get more and more pricy over time compared to
just buying a real device.

## System Design Problem

Design bird.social

- Users can sign up for an account
- Users can mark their account as private or public
- Users can follow other users; users with private profiles get a follow request
- Users can approve requests by other users to follow them
- Users can view a list of posts by users they follow
- Users can like other users' posts
- Users can repost other users' posts
- Users can set their feeds to either be live, or refreshed manually
- Users can view public profiles, getting a list of posts
- Users can view private profiles they follow
- Users can block other users
- Users can report other users
- Users can be moderators
- Moderators can delete other users' posts
- Moderators can suspend other users' accounts
- Users can be administrators
- Administrators can promote other users to moderators and administrators
- Administrators can view private profiles
