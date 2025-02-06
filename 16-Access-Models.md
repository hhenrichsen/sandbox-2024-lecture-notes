# Lecture 16 - Access Models

<!-- START doctoc generated TOC please keep comment here to allow auto update -->

- [Lecture 16 - Access Models](#lecture-16---access-models)
  - [Links](#links)
  - [Events](#events)
  - [News](#news)
  - [Access Models](#access-models)
    - [Owner-Only (eCommerce Customers)](#owner-only-ecommerce-customers)
      - [Structure](#structure)
    - [Member and Admin (Slack, Notion)](#member-and-admin-slack-notion)
      - [Structure](#structure-1)
    - [Premade Roles (Google Docs, Lucid)](#premade-roles-google-docs-lucid)
      - [Structure](#structure-2)
    - [Custom Roles (Discord)](#custom-roles-discord)
      - [Structure](#structure-3)
  - [Accounts and Organizations](#accounts-and-organizations)
  - [GUIDs vs Serial IDs](#guids-vs-serial-ids)
  - [Modelling Exercise](#modelling-exercise)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Links

- [Feedback Form](https://docs.google.com/forms/d/e/1FAIpQLSdcu-u0LD5kB9rhOcA7E1ZCw6w05RlejzrFrRALEz7krkLjVQ/viewform?usp=sf_link)
- [Q&A Form](https://docs.google.com/forms/d/e/1FAIpQLSd4c3JqKFSybays7xUNk3EeiUaDak7XvRqRyosng0ATCZf2bQ/viewform?usp=sf_link)
- [Office Hours](https://calendly.com/hhenrichsen)

## Events

- [HackUSU](https://huntsman.usu.edu/hackusu/) - February 28th - March 1st

## News

## Access Models

Some apps require more complex role management. I recommend keeping things
simple for as long as possible since it makes it easier to keep things secure.
The same applies for the number of resources that affect access.

For example, most eCommerce software is pretty simple: users can join and manage
their own orders according to some rules. They can read their own data and write
some of it.

On the other hand, think of Google Drive. You have a nested folder structure
where each folder can give access to a different set of users. Each file can
also be shared individually. Businesses can even create shared drives that
belong to the whole account.

### Owner-Only (eCommerce Customers)

This is the most common because it's the most simple, in terms of security,
system design, and UI design. In an owner-only model, users own their data and
can't access anyone else's. Everything is done through a single query based on
User ID.

#### Structure

| `Resource`       |
| ---------------- |
| `id: serial`     |
| `owner: user.id` |
| `content: text`  |

I recommend sticking with something like this for as long as you can; it makes
RLS easy should you chose to do it.

### Member and Admin (Slack, Notion)

This is useful for when an app is designed for cooperative users without much
hierarchy, like Slack. Slack uses a system where admins have all capabilities,
and users within a workspace have some subset of those.

It adds complexity now because you need a many-to-many relationship between
resources and users. This one is one of the most common, and can be fairly
simple to implement. If you decide to do this on a system that requires RLS,
it's worth reading up on
[this](https://supabase.com/docs/guides/troubleshooting/rls-performance-and-best-practices-Z5Jjwv#5-always-optimize-join-queries-to-compare-row-columns-to-fixed-join-data)
(and also
[this](https://catjam.fi/articles/next-supabase-what-do-differently?utm_source=hhenrichsen-lecture-notes)).

#### Structure

| Resource        |
| --------------- |
| `id: uuid`      |
| `owner: serial` |
| `content: text` |

| ResourceMembership         |
| -------------------------- |
| `id: serial`               |
| `resource_id: resource.id` |
| `user_id: user.id`         |
| `is_admin: bool`           |

### Premade Roles (Google Docs, Lucid)

Where you need a little more granularity, premade roles can be a good solution
to the access control problem. Now users' relationships with resources also
include information about what permission level they have. A simple structure
like the following can work, but use caution when comparing roles. If you
compare the role IDs directly, you shoehorn yourself into not being able to add
lower or higher roles.

Instead, I recommend attaching a permissions structure to each role like the one
we'll talk about next, and explicitly inheriting each permission. This will
allow you more flexibility if you decide you need more roles.

#### Structure

| Resource        |
| --------------- |
| `id: uuid`      |
| `owner: serial` |
| `content: text` |

| ResourceMembership         |
| -------------------------- |
| `id: serial`               |
| `resource_id: resource.id` |
| `user_id: user.id`         |
| `role_id: short?`          |

| `Role` (enum) |
| ------------- |
| `editor`      |
| `commenter`   |
| `viewer`      |

### Custom Roles (Discord)

Complex problems (and customization) require complex solutions. Some things
(like online communities) generally need more hierarchy. Discord is an extreme
case here, but rather than premaking certain roles, instead you leave that up to
users. This is more flexible, but can be harder to understand and is one of the
most complicated systems to implement properly.

#### Structure

| Resource        |
| --------------- |
| `id: uuid`      |
| `owner: serial` |
| `content: text` |

| ResourceMembership                   |
| ------------------------------------ |
| `id: serial`                         |
| `resource_id: resource.id`           |
| `user_id: user.id`                   |
| `role_id: role.id`                   |
| `permission_overrides: integer(128)` |

| `Role`                      |
| --------------------------- |
| `id: serial`                |
| `permissions: integer(128)` |
| `name: text`                |
| `color: varchar(6)`         |

Permissions is a bit mapping that can then be used to determine quickly if a
user has permission. For example, here's what Discord's looks like:

| `Permission` (enum)   | Bits   | Binary        |
| --------------------- | ------ | ------------- |
| `CreateInstantInvite` | `1`    | `00000000001` |
| `KickMembers`         | `2`    | `00000000010` |
| `BanMembers`          | `4`    | `00000000100` |
| `Administrator`       | `8`    | `00000001000` |
| `ManageChannels`      | `16`   | `00000010000` |
| `ManageGuild`         | `32`   | `00000100000` |
| `AddReaction`         | `64`   | `00001000000` |
| `ViewAuditLog`        | `128`  | `00010000000` |
| `PrioritySpeaker`     | `256`  | `00100000000` |
| `Stream`              | `512`  | `01000000000` |
| `ViewChannel`         | `1024` | `10000000000` |

```ts
if ((user.permissions & ViewChannel.bits) > 0) {
  // User has permission
}
```

## Accounts and Organizations

Sometimes you need larger groups of users than an individual user can supply. I
find these are pretty similar to user management, but get complex when they
share slugs like GitHub. For example, I own my `hhenrichsen` account, and am a
member of `motion-canvas`. One is an individual account, and one is an
organization.

You might need a structure like this to help resolve that, combined with a
unique constraint on slug and a check to make sure one of the org and user are
set:

| Slugs                      |
| -------------------------- |
| `id: serial`               |
| `org_id: organization.id?` |
| `user_id: user.id?`        |
| `slug: varchar(64)`        |

## GUIDs vs Serial IDs

One question that I've been asked a couple times is "when should I use a
GUID/UUID versus a numeric ID?". To me, that comes down to how accessible the ID
is to the user. For things that become artifacts that other people get access to
(Notion Notes, Google Docs, Lucidchart Documents), I generally defer to some
GUID type. For internal IDs, I generally defer to incrementing IDs.

## Modelling Exercise

Today we're going to design a code sharing app, akin to Pastebin or GitHub
Gists. I'm interested in the classes/database tables that make up this project
and the endpoints available.

Here are our requirements:

- Allow users to sign up
- Allow users to sign in
- Users should be able to create snippets, which contain 1 or more code blocks
  inside.
- Users should be able to collaborate on snippets (as added by the original
  creator).
- Users should be able to comment on code snippets.
- Users should be able to list snippets created by a specific user.
- Users should be able to mark snippets as private, excluding them from listing.

Bonus requirements:

- Users should be able to create organizations which contain 1 or more users.
- Users should be able to create snippets within organizations.
- Users should be able to list snippets created by an organization.
