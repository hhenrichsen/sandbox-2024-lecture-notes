<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [Lecture 12 - Spring Syllabus, Services, and Problem Modelling](#lecture-12---spring-syllabus-services-and-problem-modelling)
  - [Links](#links)
  - [Events](#events)
  - [News](#news)
  - [Syllabus](#syllabus)
    - [Schedule](#schedule)
    - [Communication](#communication)
      - [Feedback](#feedback)
      - [Use of AI](#use-of-ai)
      - [Office Hours](#office-hours)
    - [Assignments](#assignments)
      - [Assignment Policy](#assignment-policy)
      - [Assignment Descriptions](#assignment-descriptions)
        - [Final Check In (72pts / 60%)](#final-check-in-72pts--60)
        - [CI / CD (12pts / 10%)](#ci--cd-12pts--10)
        - [Deployed App (12pts / 10%)](#deployed-app-12pts--10)
        - [HackThisSite (Extra Credit) (6pts / 5%)](#hackthissite-extra-credit-6pts--5)
        - [Participation (12pts / 10%)](#participation-12pts--10)
        - [Attendance (12pts / 10%)](#attendance-12pts--10)
    - [Honor Code](#honor-code)
    - [Preventing \& Responding to Sexual Misconduct](#preventing--responding-to-sexual-misconduct)
    - [Student Disability](#student-disability)
  - [High Level Review](#high-level-review)
  - [Checkpoint - Any Questions?](#checkpoint---any-questions)
  - [Exercise](#exercise)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# Lecture 12 - Spring Syllabus, Services, and Problem Modelling

## Links

- [Feedback Form](https://docs.google.com/forms/d/e/1FAIpQLSdcu-u0LD5kB9rhOcA7E1ZCw6w05RlejzrFrRALEz7krkLjVQ/viewform?usp=sf_link)
- [Q&A Form](https://docs.google.com/forms/d/e/1FAIpQLSd4c3JqKFSybays7xUNk3EeiUaDak7XvRqRyosng0ATCZf2bQ/viewform?usp=sf_link)
- [Office Hours](https://calendly.com/hhenrichsen)

## Events

- [HackUSU](https://huntsman.usu.edu/hackusu/) - February 28th - March 1st

## News

## Syllabus

### Schedule

| Date          | Topic                                    |
| ------------- | ---------------------------------------- |
| January 9th   | Syllabus, Services, and Problem Modeling |
| January 16th  | Hackathon Day 1 – Class Project          |
| January 23rd  | Project Organization                     |
| January 30th  | Q&A Day                                  |
| February 6th  | Access Models                            |
| February 13th | Industry Day - Interviewing, Policies    |
| February 20th | CI / CD Revisited                        |
| February 27th | Q&A Day                                  |
| March 6th     | Guest Lecture                            |
| March 13th    | Building for Scale                       |
| March 20th    | Headers, Caching, CDNs                   |
| March 27th    | Q&A Day                                  |
| April 3rd     | Guest Lecture                            |
| April 10th    | Retrospective                            |

### Communication

The real world runs on communication. You can't skip meetings or miss deadlines
without letting the interested parties know. I expect the same out of you – talk
to me, let me know what adjustments or accommodations you need as they arise.
I'll be as flexible as I can be with deadlines as long as you talk to me in
advance. Afterwards, I feel the need to do those who were on time justice by
adding a penalty to those who are late.

I'm available via email and Slack for this class. I get notifications from Slack
more frequently than email.

#### Feedback

I am still fairly new to teaching (it's not my first rodeo because it's my
second), and I want this class to be as useful to you as possible. To that end,
if you have feedback for me, whether that's something you want me to adjust,
start doing, or stop doing, please let me know, either by one of the above
communication channels or via
[this anonymous form](https://docs.google.com/forms/d/e/1FAIpQLSdcu-u0LD5kB9rhOcA7E1ZCw6w05RlejzrFrRALEz7krkLjVQ/viewform?usp=sf_link).

#### Use of AI

Please do not use AI to communicate with me. AI normally results in much more
verbose communication, and I prefer to get straight to the point. I have worked
enough with AI that I can generally tell, and I worry about situations like this
becoming more and more common:

![[Pasted image 20240721103512.png]]

#### Office Hours

Office hours are available by appointment via Calendly. The link is bookmarked
in the Slack channel.

### Assignments

#### Assignment Policy

Assignments can be turned in late for half credit. If you are concerned about
the deadline / have other things going on, sandbox-related or otherwise, please
let me know beforehand and we can figure out an equivalent or more
individualized deadline.

If you think you've done something equivalent already, reach out and let's talk
about it. I'd rather you focus on building, and give you credit where it's due.

#### Assignment Descriptions

##### Final Check In (72pts / 60%)

Due: Wednesday, Apr 23 at 11:59 pm

There are two options for this. With the other engineers on your team, make an
appointment during office hours, or submit a recording at your leisure. If
you're submitting a video, each member of your group should submit it.

Either way, we'll talk about these things:

- What have you built? Show off some key features of your final project, and any
  demos of ideas that didn't work out.
- What technical problems have you solved?
- What's the most important thing you've learned this year?
- How do you guarantee that users maintain access to their data? How do you
  guarantee that other users don't get access to others' data?
- Are you proud of what you've built?

**Point Breakdown:**

- 24pts - Answering the Initial Questions
- 24pts - Demonstrating a Production Application
- 16pts - Answering any follow-up questions (this is done as part of the office
  hours meeting, or I'll follow up on Slack if there are any)

##### CI / CD (12pts / 10%)

Due: Friday, Feb 28 at 11:59 pm

If you are building a web / mobile app, build a CI pipeline into your app that
does the following (4 points each):

- Format / Linting according to some style guide (can be your own configuration)
- Run Tests
- Build / Compile the App (if your app is not in a compiled language, it can be
  worth checking types or using a tool like py_compile that verifies that the
  pushed app will be able to be run).

If you are not building a web / mobile app, work with me and we can try to find
something comparable.

Submit a link to your repo. Make sure it's shared with hhenrichsen on GitHub.

##### Deployed App (12pts / 10%)

Due: Friday, Mar 28 at 11:59 pm

Add a step to your CI pipeline that deploys your app. Ideally, this should
include deployment to some kind of staging environment, and then the ability to
promote that to production, however this assignment will only require that there
be a process to go from code to deployed.

This does not need to be the same app as the previous assignment, nor does it
need to be the current app you are working on.

If you are not building a web / mobile app, work with me and we can try to find
something comparable.

Submit a link to your deployed app, or to the repo that has the workflow that
deploys your app.

##### HackThisSite (Extra Credit) (6pts / 5%)

Due: Wednesday, Apr 23 at 11:59 pm

Complete the Basic and JavaScript tracks on HackThisSite for 3 points each.
Submit a link to your profile. Leave a comment on your profile mentioning
Sandbox 04.

##### Participation (12pts / 10%)

Due: Wednesday, Apr 23 at 11:59 pm

Here are some options for participation:

- Give a Tech Talk (12 points)
- Ask questions in class (4 points)
- Come in for office hours outside of check-ins (3 points)
- Talk about news at the beginning of class (2 points)
- Submit topics to the Q&A sessions (2 points)
- Submit the participation activity in each class session (1 point)

This is available all semester, so there is no makeup for this assignment. This
is easier to do during class than out of it.

##### Attendance (12pts / 10%)

Due: Wednesday, Apr 23 at 11:59 pm

### Honor Code

In keeping with the principles of the BYU Honor Code, students are expected to
be honest in all of their academic work. Academic honesty means, most
fundamentally, that any work you present as your own must in fact be your own
work and not that of another. Violations of this principle may result in a
failing grade in the course and additional disciplinary action by the
university. Students are also expected to adhere to the Dress and Grooming
Standards. Adherence demonstrates respect for yourself and others and ensures an
effective learning and working environment. It is the university's expectation,
and every instructor's expectation in class, that each student will abide by all
Honor Code standards. Please call the Honor Code Office at 422-2847 if you have
questions about those standards.

### Preventing & Responding to Sexual Misconduct

_The health and well-being of students is of paramount importance at Brigham
Young University.  If you or someone you know has experienced sexual harassment
(including sexual violence), there are many resources available for assistance._

In accordance with Title IX of the Education Amendments of 1972, BYU prohibits
unlawful sex discrimination, including sexual harassment, against any
participant in its education programs or activities. The university also
prohibits sexual harassment by its personnel and students. Sexual harassment
occurs when

- a person is subjected to unwelcome sexual speech or conduct so severe,
  pervasive, and offensive that it effectively denies their ability to access
  any BYU education program or activity;
- any aid, benefit, or service of BYU is conditioned on a person’s participation
  in unwelcome sexual conduct; or
- a person suffers sexual assault, dating violence, domestic violence, or
  stalking on the basis of sex.

University policy requires all faculty members to promptly report incidents of
sexual harassment that come to their attention in any way, including through
face-to-face conversations, a written class assignment or paper, class
discussion, email, text, or social media post. Incidents of sexual harassment
should be reported to the Title IX Coordinator
at [t9coordinator@byu.edu](mailto:t9coordinator@byu.edu) or (801) 422-8692 or
1085 WSC. Reports may also be submitted online
at [https://titleix.byu.edu/report](https://titleix.byu.edu/report) or
1-888-238-1062 (24-hours a day).

BYU offers confidential resources for those affected by sexual harassment,
including the university’s Sexual Assault Survivor Advocate, as well as a number
of non-confidential resources and services that may be helpful. Additional
information about Title IX, the
university’s [Sexual Harassment Policy](https://policy.byu.edu/view/index.php?p=155&s=s879),
reporting requirements, and resources can be found
at [http://titleix.byu.edu](http://titleix.byu.edu/) or by contacting the
university’s Title IX Coordinator.

### Student Disability

Brigham Young University is committed to providing a working and learning
atmosphere that reasonably accommodates qualified persons with disabilities. A
disability is a physical or mental impairment that substantially limits one or
more major life activities. Whether an impairment is substantially limiting
depends on its nature and severity, its duration or expected duration, and its
permanent or expected permanent or long-term impact. Examples include vision or
hearing impairments, physical disabilities, chronic illnesses, emotional
disorders (e.g., depression, anxiety), learning disorders, and attention
disorders (e.g., ADHD). If you have a disability which impairs your ability to
complete this course successfully, please contact the University Accessibility
Center (UAC), 2170 WSC or 801-422-2767 to request a reasonable accommodation.
The UAC can also assess students for learning, attention, and emotional
concerns. If you feel you have been unlawfully discriminated against on the
basis of disability, please contact the Equal Opportunity Office at
801-422-5895, [eo_manager@byu.edu](mailto:eo_manager@byu.edu), or
visit [https://hrs.byu.edu/equal-opportunity](https://hrs.byu.edu/equal-opportunity) for
help.”

## High Level Review

One of the questions I got last semester was to go through what we covered at a
high level. So let's go point by point:

[Diagram Here](https://lucid.app/lucidchart/f87108b1-1422-4120-8dcb-41dc5f59d4ab/view)

## Checkpoint - Any Questions?

## Exercise

I want to do some smaller-scoped problems at a high level in this class to help
us stay more grounded. I'm not sure what interval I want to do these at yet, but
I figured now would be a good time to try it out.

Requirements:

- Allow unregistered users to follow short links
- Allow users to sign up and sign in
- Allow users to create, edit, and delete their own links

Deliverables:

- A list of classes, the data associated with them, and the relationships
  between them
- A list of backend routes and a basic description of their use
