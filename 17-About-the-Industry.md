# Lecture 17 - Career Advice

<!-- START doctoc generated TOC please keep comment here to allow auto update -->

- [Lecture 17 - Career Advice](#lecture-17---career-advice)
  - [Links](#links)
  - [Events](#events)
  - [News](#news)
  - [OAuth Revisited](#oauth-revisited)
  - [Job Search Advice](#job-search-advice)
    - [On Technical Interviews](#on-technical-interviews)
      - [Knowledge Checkpoints](#knowledge-checkpoints)
      - [Solution-Building](#solution-building)
      - [Take-Home Projects](#take-home-projects)
    - [On Behavioral Interviews](#on-behavioral-interviews)
    - [On Doing Your Research](#on-doing-your-research)
    - [On Passion](#on-passion)
    - [On Asking Questions](#on-asking-questions)
  - [Career Advice](#career-advice)
    - [On You Vs Your Work](#on-you-vs-your-work)
    - [On Setting Your Standards](#on-setting-your-standards)
    - [On Leadership and Initiative](#on-leadership-and-initiative)
  - [Structuring a Company](#structuring-a-company)
    - [On Being a Founder](#on-being-a-founder)
    - [On Picking a Target Industry](#on-picking-a-target-industry)
    - [On Forming and Scaling Teams](#on-forming-and-scaling-teams)
    - [On Commitment](#on-commitment)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Links

- [Feedback Form](https://docs.google.com/forms/d/e/1FAIpQLSdcu-u0LD5kB9rhOcA7E1ZCw6w05RlejzrFrRALEz7krkLjVQ/viewform?usp=sf_link)
- [Q&A Form](https://docs.google.com/forms/d/e/1FAIpQLSd4c3JqKFSybays7xUNk3EeiUaDak7XvRqRyosng0ATCZf2bQ/viewform?usp=sf_link)
- [Office Hours](https://calendly.com/hhenrichsen)

## Events

- [HackUSU](https://huntsman.usu.edu/hackusu/) - February 28th - March 1st
- Student Ratings - should be opening soon. Please do them and be honest. The
  feedback is useful to me and, if positive, gives reason to hire me again.

## News

## OAuth Revisited

I've had a couple questions in the past week about OAuth, so I thought it would
be prudent to revisit it.

In general, I think your auth should be pretty straightforward. Unless you're
noticing high numbers of requests or other bottlenecks, I wouldn't worry about
context providers or other things like that. Those can be useful, but I've found
they take some time and research to be done properly.

The overall flow is fairly simple, at least to authenticate a user in the first
place. It should go something like this:

1. Your user clicks on a **link** on your page that sends them to either your
   backend (to generate an OAuth URL), or a specific OAuth URL that you've
   pre-generated. In my app's case, it either goes to
   `accounts.google.com/o/oauth2/auth` or `discord.com/oauth2/authorize`,
   depending on the provider.
2. Your user is sent to a provider by clicking that link, and goes through a
   third party sign-in flow.
3. Your user comes back to a URL with a token. In my case, I have URLs like
   `hvz.gg/auth/google/callback` and `hvz.gg/auth/discord/callback`, depending
   on the provider.
   1. You then make a call out to your provider to get information about that
      user using the token. For me, I do a request to Discord or Google,
      depending on what callback I'm getting.
   2. You respond to the user, setting a cookie (or other session management
      information), and redirecting them to some other page.
4. From then on, the user has a cookie to identify themselves that should map to
   something on your server. Many libraries will do this for you, and this is a
   complexity that I'm not super fond of managing.

Since most of you are doing things with Supabase, and at least some of that
population are doing things with Next.js, those are the two that I've chosen to
talk about.

With Supabase, the primary differences are:

- The first step goes to Supabase instead of your backend.
- The callback and request for details are also handled by Supabase, and may be
  inserted into the users table.
- The resulting session is stored and managed in the Supabase-js library for
  you.

## Job Search Advice

### On Technical Interviews

Most interviews that I've seen fall under three categories: knowledge
checkpoints, solution-building, and take-home projects.

#### Knowledge Checkpoints

Knowledge checkpoints the most common. These are the LeetCode-style questions,
where the goal is to see how well you know an optimization or logical concept.
Some knowledge checkpoints will be rote, where some will check for more in-depth
knowledge by being more unique or involved.

Some of these are whiteboard-type programming problems, and some of these will
require you to write code that can actually run.

For these, I recommend knowing your optimization strategies, including:

- Greedy Algorithms
- Dynamic Programming
- Divide and Conquer

Hackerrank, CodeWars, and LeetCode all have problems for these types of
knowledge. I think there's a certain amount of practice required to pass these
types of interviews, but it's not worth grinding LeetCode constantly.

#### Solution-Building

Next are solution building interviews. These are ones where you're given a
unique (or at least, custom-built for the people interviewing) problem. These
include system design exercises where you need to talk about infrastructure, and
domain modelling problems where you talk about how you would structure data and
classes.

I've been doing a combination of those two in the modelling exercises for this
class.

For these, I think practical experience is best, both in terms of solving
real-world problems, and in terms of communicating in terms of data without
functionality.

#### Take-Home Projects

I haven't personally run into these, but I've heard about them. You're given a
template (or told to start from scratch) and asked to build a project to some
specification.

These are the most real-world, but tend to be the most work. I find smaller
companies run these more frequently. In these, it's a balancing act between not
overengineering things but abstracting and testing enough. I find clarifications
towards those things are useful up front, if possible.

There's also a variant of these that I've heard about where you can do onsite
development, or pair program with a current employee there. These are tricky
because sometimes you can end up doing uncompensated work, but they can also be
better indications of your skill.

### On Behavioral Interviews

There's normally a behavioral interview or two thrown in the mix too, where you
talk about your experience and personality and such. I've found the best way to
go about these is to be genuine, and bring the same personality and ideas that
you're going to be bringing to work.

### On Doing Your Research

One tool I've found useful while looking for a job is researching the company
and culture they talk about. I don't go overkill, but being able to speak on the
same terms in terms of behavior can be super useful. Many times, behavioral
interviews are also about culture fit, and so knowing what to emphasize in terms
of resume and experience during the interview can be a super useful tool.

### On Passion

I think the best career advice I ever received was that you should do your
research before an interview, and make sure it's a place that you'd be
interested and excited to work. Once you're in an interview, that excitement
will show through.

To me, this works twofold with passion. If the interviewers can see from the way
you talk and act that you'd enjoy the type of work they do, it's much much
easier for them to want to hire you to do that work. It ties into a number of
biases that interviewers will have, the most powerful of which is to fill the
spots they have open. If they fill the spot with a passionate, qualified person,
they don't have to do more interviews.

### On Asking Questions

You not only be answering questions when you do an interview. Just as the
interviewers are trying to determine if you're a good fit with their company,
you should be trying to determine if the company is a good fit for you.

Some questions I think about asking:

- What does it take for an individual to be successful?
- How are employees acknowledged for good and bad work?

## Career Advice

### On You Vs Your Work

One of the hardest lessons for me was learning that suggestions and commentary
on my work were not suggestions or commentary on myself. I eventually learned,
and had to swallow my pride a bunch of times, but I think I'm a much better
engineer now because of that.

A similar lesson I had to learn was that much of the time, it's more efficient
to ask for help. My math goes something like this:

- The time it will take me to solve and understand this on my own is probably
  multiple hours.
- The time it will take me to make a well-formed question is 1-2 minutes.
- The time it will take me to double check my assumptions and show my work in
  the question is 5-10 minutes.
- If I interrupt someone, they'll be out of the flow state for 15-20 minutes
  max. If they're not in the flow state, I haven't impacted their flow state at
  all.
- I also have to trust that if someone wants to maximize their flow state, they
  have control over their notifications (or headphones, or if the door to their
  office is open, etc.)
- If I get an answer, it will take me 20-30 minutes to implement the answer.
- Therefore, if I interrupt someone, I'm still saving multiple hours overall.
  Asking the question is nearly always worthwhile.

There's a balance there between asking for solutions too quickly, and asking for
help. What I've found works well for me is to do some upfront work to make sure
I understand enough to ask a question and make a proposal, or at least know
enough to ask the question.

### On Setting Your Standards

When you first start working somewhere, you set others' expectations for
yourself. If you start off diving deep into issues and considering alternative
solutions, that's what people will continue to expect from you. If you get your
work done and talk to no one, that's what will be expected. If you ask good
questions and solve hard problems, you'll continue to be given hard problems.

Set a standard that's sustainable for yourself, but at the same time make sure
you're setting one that will help you grow (and ideally, become invaluable).

### On Leadership and Initiative

I think early on in your career, the best way to continue to learn and grow is
to get involved and dig into hard things, especially in things you're interested
and passionate about. I think saying yes to new responsibilities by default is
useful until you feel stretched beyond what you can accomplish. Then it's time
to say no, or time to transition smaller tasks to other new folks who are
potentially looking for those opportunities.

There are usually more responsibilities available than the ones assigned to you,
and the simple act of asking can unlock those for you. These can be anywhere
from super simple things, like helping to file tickets and manage work on a
team, or can be complex, like getting involved in making sure production systems
stay up and running.

I've found that I learned the most not when it was for me, but when I had others
who depend on me as well. I had to be much more involved in the processes on my
team, and much more intentional about how I spent my time because there were
more demands on it. But most of all, if I messed up, the stakes were higher and
would affect other people.

## Structuring a Company

### On Being a Founder

As an initial member of a company, your attitudes and philosophies will be
inherited by those that follow after you. This is true in that some of those
philosophies will make their way into your codebase and processes, but also true
in that those who report and look up to you will seek to emulate behaviors that
you exhibit.

This is also part of why you'll sometimes see an exodus of individuals after
company leaders change: they worry that because a leader whose policies and
philosophies they agreed with is gone, a new one will come in and take their
place with different policies.

I recommend being intentional here. I've seen and heard about places where
people weren't careful about the culture and standards they were setting, and it
ending up being expensive (or fatal for the company) in the long run.

After you reach enough people where core values mean something and will affect
others' behavior, I think it's worth capturing and making those public. What do
you want to be? How do you want people to behave? It's then up to you to make
sure everyone else is being intentional about those things, too.

### On Picking a Target Industry

When you pick an industry to sell to, you need to be prepared to play at that
industry's pace:

- **Individuals and Startups** - these will be the most bleeding-edge and
  fastest to adapt things. A passionate user can make the decision to buy your
  product on their own, and the cycle time is generally days.
- **Teams and Small Businesses** - Teams and small businesses can adapt tools
  that individuals are passionate about, but normally take time to adopt new
  things since there are more approvers in the process. Passionate individuals
  can become champions to help approvers make the decision. The cycle time here
  is generally weeks to months.
- **Enterprises** - Enterprises move slowly, especially as their size grows. The
  cycle time here is usually years for smaller enterprises, and can even be
  decades for larger ones. Normally, you need either executives to be bought in
  on your product, or you need an overwhelming number of champions.
- **Government** - Government is stable, but that means it takes a long time for
  them to adapt technologies. There are exceptions to this, especially on a
  local level, but for the majority of use cases I find it best to choose this
  industry carefully. Unless you have ridiculous amounts of capital to burn
  waiting for change here, the main move that I've seen here is larger B2B
  products eventually building support for the requirements that governments
  need.

### On Forming and Scaling Teams

I think in general, teams should be small. I think about 5 engineers is the max
before a team starts running into issues, and below 2 it's not really a team.
Teams can cross-collaborate, but ideally their work shouldn't be sequenced based
off of another team. Or in other words, Team A's work shouldn't have a hard
dependency on Team B if you can avoid it.

Management-wise, I think flat organization structures are better than deep ones.
However you define "leadership", they should be accessible to the individual
contributors, and decisions shouldn't take layers and layers of buy-in. By the
point where you have more than two layers of people involved in a project
directly, or three layers indirectly, there are too many cooks and driving
alignment takes a long time.

This spot is actually a wrinkle for organizations that grow quickly, because
many of those who become directors, vice presidents, and executives are those
who were once individual contributors. Some of them will have a hard time
learning how to be hands-off on projects and trust the people they hire.

### On Commitment

I have seen very successful products come from unexpected places, and as I'm
sure you've heard from others, the primary thing that I've seen lead to success
is commitment. This will mean different paths for different people.

Some find success by putting in tons of work in a short amount of time, and
finding the means to support themselves through that work.

Some find success by taking a longer path, sometimes splitting their time
between supporting themselves and pursuing their idea or dream.

Some find success by accident, lucking into a strong enough market that allows
them to continue building.

Some find success by tripping and stumbling repeatedly towards their objective,
and either find a better outcome than their original objective, or through
effort reach their original goal.

The most important thing that they all do, though, is that they keep trying. I
would posit that there are more problems than people in the world, and that many
of those are worth solving. The key factor in any circumstance is effort.
