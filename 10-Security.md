# Lecture 10 - Security

<!-- START doctoc generated TOC please keep comment here to allow auto update -->

- [Lecture 10 - Security](#lecture-10---security)
  - [Links](#links)
  - [Resources and AAA](#resources-and-aaa)
    - [Resources](#resources)
    - [Authentication](#authentication)
    - [Authorization](#authorization)
    - [Accounting](#accounting)
  - [Types of Attacks](#types-of-attacks)
    - [Supply Chain Attacks](#supply-chain-attacks)
    - [Third Party Vulnerabilities](#third-party-vulnerabilities)
    - [Secrets Leaking](#secrets-leaking)
    - [Hack This Site](#hack-this-site)
    - [Your Own Audits and Bug Bounties](#your-own-audits-and-bug-bounties)
  - [Staying up to Date with Security](#staying-up-to-date-with-security)
    - [Courses and Resources](#courses-and-resources)
    - [Interactive](#interactive)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Links

- [Feedback Form](https://docs.google.com/forms/d/e/1FAIpQLSdcu-u0LD5kB9rhOcA7E1ZCw6w05RlejzrFrRALEz7krkLjVQ/viewform?usp=sf_link)
- [Q&A Form](https://docs.google.com/forms/d/e/1FAIpQLSd4c3JqKFSybays7xUNk3EeiUaDak7XvRqRyosng0ATCZf2bQ/viewform?usp=sf_link)
- [Office Hours](https://calendly.com/hhenrichsen)

## Resources and AAA

### Resources

I'm going to talk a lot about "resources" in this lecture. This is an abstract
term for "things that users can access." That might mean REST endpoints, certain
pages, and really anything else that may have access controls attached.

### Authentication

Authentication is your ability to identify a user based on the request that they
are making. Some endpoints may only make sense in relation to a current user,
for example an endpoint that shows the currently logged in user, or lists
resources owned by that user.

### Authorization

Authorization is your ability to restrict resources to specific users.
Authorization issues are much more common than authentication issues, especially
as you add more and more resources with more complex access relationships.

### Accounting

Accounting (or auditing) is information that you store that allows you to see a
sequence of events that users are taking. One example is an audit log, where you
keep a history of any administrative actions taken, or where you can show what
activities users are doing. This is useful when something goes wrong in
authorization; you can still use authentication to establish a list of resources
that were improperly accessed.

Normally, an access log with a user ID will be sufficient, but this may vary by
your use case.

## Types of Attacks

The OWASP has a list [here](https://owasp.org/Top10/) that's worth being
familiar with. Here are some others that I think are worth thinking about.

### Supply Chain Attacks

These are vulnerabilities introduced to upstream dependencies like npm
dependencies. Some people get around these by pinning versions or vendoring
packages. The recent `xz` vuln is an example of this.
[This is another example of a theoretical supply chain attack](https://david-gilbertson.medium.com/im-harvesting-credit-card-numbers-and-passwords-from-your-site-here-s-how-9a8cb347c5b5)
that I think about a lot.

### Third Party Vulnerabilities

A variant of Supply Chain Attacks, but ones that I attribute to incompetence and
ignorance more than malice. Sometimes third parties, especially third party
services that you use, or even customers, do insecure things. You should be
careful of what third parties are allowed to do on your services and have a plan
in place should one of them stop working.

### Secrets Leaking

Through a number of other things that could go wrong, sometimes your secrets get
exposed to the public. You should have a plan in place (and perhaps practice)
rotating your secrets.

### Hack This Site

Please go through the exercises on the site; they show you some of the many ways
people can get to information on your app. I think the DEFCON talks are worth
watching / listening to as well to learn about how things are breached. There
are also security and exploit reports worth reading, and learning from other
people's mistakes.

### Your Own Audits and Bug Bounties

It's definitely worth it to do something like we did last week on your own site
– get together, try to find vulnerabilities, or just otherwise explore what
information you can get access to.

You can also do bug bounties, and there are individuals out there who will look
for vulnerabilities in your apps in exchange for a fee for each one they find.

## Staying up to Date with Security

Here are some ideas from me and one of my friends who has spent some time
pentesting.

### Courses and Resources

- Read through the [OWASP Top 10](https://owasp.org/Top10/) and become familiar
  with the most common vulnerabilities. Since these are the most common, these
  are also some of the things that attackers will look for.
- Learn about the exploits and processes that are out there for analyzing and
  breaking into apps. The [MITRE ATTACK](https://attack.mitre.org/) framework is
  one good resource to learn about different methods attackers will use at
  different phases of a breach.
- Look for vulnerability reports and blog posts about getting into libraries and
  3rd parties that you're using–Dependabot and similar tools should do this for
  you, at least for code libraries. A misconfiguration in a Supabase or Firebase
  can lead to full access to all of your data if you're not careful, and that's
  not just limited to those tools.
- Major breaches tend to make the news, as well. Here are some examples:
  - [xz](https://arstechnica.com/security/2024/04/what-we-know-about-the-xz-utils-backdoor-that-almost-infected-the-world/)
  - [Log4J](https://blog.cloudflare.com/inside-the-log4j2-vulnerability-cve-2021-44228/)
  - [Cloudbleed](https://blog.cloudflare.com/quantifying-the-impact-of-cloudbleed/)
  - [Heartbleed](https://owasp.org/www-community/vulnerabilities/Heartbleed_Bug)
- [DEFCON](https://www.youtube.com/@DEFCONConference) talks are an interesting
  resource, and cover many different aspects from data recovery and trolling
  potential malicious users to picking locks and breaking into elevator
  controls. In my experience, these are useful ways to quickly learn the basics
  about something, and then I can find other resources and more in-depth talks
  to look into on my own.

### Interactive

- Try out the [OverTheWire](https://overthewire.org/wargames/bandit/)
  challenges. These are similar to HackThisSite, but more focused around Linux
  (and will teach you the basics!) as well as some of what can happen when
  someone gets into a server you control.
- I also recommend continuing with the tracks we've not done as a class on
  [HackThisSite](https://www.hackthissite.org/).
- Take a look at [HackTheBox](https://www.hackthebox.com/hacker) courses.
- Take a stab at getting into the
  [Metasploitable](https://github.com/rapid7/metasploitable3) VM, using the
  [Metasploit](https://www.metasploit.com/) framework to do attacks against the
  VM.
- Use your devtools to look into what your site is doing. What happens if you
  request different endpoints, send invalid data, or try to get access to things
  that you shouldn't.
- Use a tool like [Wireshark](https://www.wireshark.org/) to analyze traffic to
  and from your app (especially a mobile app). See what's available and what you
  can manipulate.
