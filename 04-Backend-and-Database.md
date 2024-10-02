# Lecture 04 - Backend and Databases

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

_In the future, this will be a segment where we talk about things that are going
on in the tech space that are worth knowing about. Please slack a link to a news
article about it after you get called on._

### Articles

- [A Taxonomy of Tech Debt](https://technology.riotgames.com/news/taxonomy-tech-debt)
  by Bill Clark, an engineer on League of Legends.
- [Move Fast and Abandon Things](https://engineersneedart.com/blog/movefast/movefast.html)
  by John Calhoun, a former Apple engineer.

## Demo - Responsiveness

Here's some text content without any custom styling:

![[Pasted image 20241001181601.png]]

Here's what happens when we make the boundaries wider:

![[Pasted image 20241001181636.png]]

And what happens when we squish the bounds:

![[Pasted image 20241001181650.png]]

Here's making them even smaller:

![[Pasted image 20241001181704.png]]

And if we have content that's super wide:

![[Pasted image 20241001181715.png]]

Whoa, that's eye-fatigue inducing. It takes a long time for me to move my eyes
from the right to the left. Maybe we should set a limit:

![[Pasted image 20241001182009.png]]

Hmm, but that breaks down at smaller sizes and some of the text (or other
content) goes off of the screen:

![[Pasted image 20241001182027.png]]

Instead, let's set a max width, and let it grow to that point:

![[Pasted image 20241001181726.png]]

Now when it gets small, it's acceptable to use a smaller width:
![[Pasted image 20241001182211.png]]

Sometimes, as pages get smaller, it makes sense to have elements re-flow as
well. [Flex Box](https://css-tricks.com/snippets/css/a-guide-to-flexbox/) is
super useful for this.

For example, instead of the default behavior being to squish elements, you can
have it wrap them onto a new line:

![[Pasted image 20241001185424.png]]

Or another example, having certain elements grow more than others:

![[Pasted image 20241001185459.png]]

## Backend and Databases

Your backend is your haven. Effectively everything on the frontend can be
accessed and mutated by the user. In an ideal backend, that should not be the
case. We'll talk more about how to enforce that ideal later with Security,

## Ways to Communicate

### REST and HTTP

HTTP, or Hypertext Transfer Protocol, is the protocol that the web is built on.
It's made up of verbs, like `GET`, `POST`, `PUT`, and `DELETE`. REST, or
REpresentational State Transfer, is an architecture built on top of HTTP that
dictates how resources and verbs should be constructed. For example, a RESTful
call might look like this:

```http
GET /users
```

And this might return a list of users. One implementation might look like this,
just returning a list of user IDs and allowing the client to make additional
calls for the ones they are interested in:

```json
[
  {
    "id": 1
  },
  {
    "id": 2
  },
  {
    "id": 3
  }
]
```

A better implementation might give back enough information to build an
application, avoiding
[the N+1 problem](https://restfulapi.net/rest-api-n-1-problem/), for example:

```json
[
  {
    "id": 1,
    "name": "John Doe",
    "email": "johndoe@example.com"
  }
]
```

Some libraries will also let you eagerly go and fetch portions of related
models, or you can write your own queries to do this. This can also help with
the N+1 problem, allowing you to fetch all of the data you need in one query,
both to the server, and to the database.

To mutate data, you'll use other verbs. `POST` (create without a preset ID) or
`PUT` (create _with_ a preset ID) requests are used to add data, `PATCH`
requests are used to update data, and `DELETE` requests are used to delete data.
Most requests other than `GET` requests will allow you to send data in the body
of the request, like this:

```http
POST /auth/login
{
    "username": "hunter",
    "password": "hunter2"
}
```

Some APIs communicate with other data types, like XML:

```xml
<users>
  <user>
    <id>1</id>
    <name>John Doe</name>
    <email>johndoe@example.com</email>
  </user>
</users>
```

And there are other, less frequent data types as well, like binary data.

You might run into cases where it makes sense to only have specific fields, and
many APIs will implement this by allowing you to specify the fields you want in
the query string, like this:

```http
GET /users?fields=id,name
```

You might implement this in your database like this:

```sql
SELECT id, name FROM users;
```

Or in an ORM like Prisma like this:

```ts
prisma.user.findMany({
  select: {
    id: true,
    name: true,
  },
});
```

You probably should not just pass these as raw strings to your database, as this
can open you up to SQL injection attacks. Instead, you should use a library or
ensure that you are sanitizing your inputs, or at least making sure that the
fields being requested are safe ones.

Implementing this can save you on bandwidth, but if you find yourself filtering
very frequently, you might be better off with the next type of communication,
GraphQL.

### GraphQL

GraphQL allows you to query just the data you want from a server. That means
it's a lot more flexible for unstructured data, but it means that if you are
using a structured data source (like a SQL database), you will need to do
complex queries to get the data you want.

That means that when using GraphQL, you might want to consider using a document
data store like MongoDB and expose the entire documents to the GraphQL server,
or use a graph database like Neo4j.

GraphQL queries look like this:

```graphql
query {
  users {
    id
    name
    email
  }
}
```

Some servers will allow you to make the request as a query, especially when
serving the GraphQL server over HTTP:

```http
/query?={users{id,name,email}}
```

To modify data, GraphQL uses mutations that are a little bit like stored
procedures in a database; you give parameters to a preset mutation, and then can
execute that mutation:

```graphql
mutation CreateUser($name: String, $email: String) {
  # this is the data you are using to create the user
  createUser(name: $name, email: $email) {
    # and this is the data you are querying back
    id
    name
    email
  }
}
```

GraphQL is largely built around having a published schema, which can result in
some really good developer experience, especially if you want to convince people
to build against your platform.

GraphQL shares some with tRPC since the schema is public and typesafe; having
both of those benefits baked into your API makes the experience of developing on
it both really fast and really easy. Things like Swagger can bring this to a
REST API if you have gone that route, but the other two methods on top of HTTP
have that automatically.

#### On Performance

GraphQL can be really nice for rapid prototyping or distributed teams, since you
don't need to specifically create new endpoints to get exactly the data you
want.

That said, from what I've seen, GraphQL generally works best with a graph-based
or document database. GraphQL queries can be tricky with a relational database
because it turns a simple request into many queries. What can be accomplished
with a join query and returning data can turn into many, many requests to get
the same data due to the transformation of the GraphQL request into queries.

### RPC, gRPC, and tRPC

RPC stands for Remote Procedure Call. It's a generic framework for describing
how a client (even if that client is a server itself) should talk to a server
(or in other words, a remote), triggering some code execution on that server, or
in other words a procedure call.

That sounds a lot like REST, and in some ways (and even some RPC
implementations) it is the same. One of the main differences is that RPC systems
generally specify a schema, a shared language that clients and servers can use,
regardless of the language they use internally. To some extent this exists in
REST and HTTP, but it's fairly loose.

#### gRPC

[gRPC](https://grpc.io/) is Google's implementation of RPC, and has been used
fairly broadly, especially when servers are talking to other servers. It uses
[protobuf](https://protobuf.dev/) under the hood to deal with serialization and
deserialization as binary, rather than other data formats like JSON or XML.

```proto

message Person {
	string name = 1;
}

message Greeting {
	string reply = 1;
}

service Hello {
	rpc Greet(Person) returns (Greeting) {}
}
```

#### tRPC

tRPC is a recent entry, but a really cool way to work quickly with schema
changes and such. It's built on top of HTTP, so it still uses the same verbs
under the hood, but takes the same structure.

It allows you to sync your schema between clients and servers, including the
type information and automatic validation. Here is what a tRPC schema looks
like:

```ts
import { prisma } from "db/lib/prisma";
import * as z from "zod";
import { publicProcedure, router } from "./trpc";

export const appRouter = router({
  postList: publicProcedure.query(async () => {
    const posts = await prisma.post.findMany();
    return posts;
  }),
  addPost: publicProcedure
    .input(z.object({ title: z.string(), content: z.string() }))
    .mutation(async (opts) => {
      const { title, content } = opts.input;
      const post = await prisma.post.create({
        data: {
          title,
          content,
        },
      });
      return post;
    }),
});

export type AppRouter = typeof appRouter;
```

I have opted to use this since it's a lot more straightforward than creating
routers, dealing with requests, etc., when the main thing I care about is
writing the actual procedures and logic. The other two generally either require
boilerplate, or require knowing about the right niche technology that can turn a
database into a REST API or a GraphQL server.

## Common Database Attributes

I'm going to call these attributes rather than types, because some of these will
dip into multiple attributes. For example, Redis is a Key / Value, In-Memory
database; and PostgreSQL is primarily a Relational database, but it can
accomplish some similar feats to a Document database via its JSON column type.

### Relational

**Examples:** PostgreSQL, mySQL, msSQL, MariaDB

Relational databases have been around for awhile, and are used pretty broadly.
The core idea is that everything is a field in a row of a table, and any
connections between data are implied from the data itself.

The fundamental units of data are:

- **Databases**, which contain schemas and tables
- **Schemas** (varies by implementation), which are an abstraction to prevent
  collisions between multiple clients on the same database, or are
  interchangeable with databases.
- **Tables**, which are composed of rows and a set of typed fields
- **Rows**, which contain values for each of the table's fields.

Since relational databases have been around for a long time, many scaling
problems have been solved with them. You can scale databases by adding more
discrete databases in a process called sharding, where certain tables are split
into databases by certain types of information, for example, post IDs in a
social network.

If your data keeps a similar shape between occurrences, a relational database is
probably a good choice.

Sometimes databases that do not have this attribute are called noSQL or
non-relational.

### Document

**Examples:** mongoDB, CouchDB, DocumentDB

Document databases have emerged as popular as an alternative to relational
databases. Instead of dealing with a fixed shape for data, document databases
store JSON documents that can have any number of arbitrary properties.

The fundamental units of data are:

- **Documents**, which are JSON that can be queried and mutated as needed.

If your data is flexible and changes by user, or doesn't fit into the rigid
types that relational database fields require, a document database may be a
better choice. Rather than spinning up duplicate databases, most document
databases are designed to scale by adding additional compute clusters.

### Graph

**Examples:** Neo4j, Neptune

Graph databases are similar to relational and document databases, however
instead of inferring relationships from the data, relationships are a
first-class part of the database.

The fundamental units of data are:

- **Nodes**, which hold a set of properties
- **Labels**, which classify a node or attach metadata
- **Edges** or **Relationships**, which link two nodes and hold a set of
  properties
- **Properties**, which store information about a given node

Because these are stored as graphs, there are optimizations to querying some of
these databases that may make sense (as well as additional query types that are
less complex in a graph), especially if there are many different types of
connections that need to be a part of your data.

Graph databases scale by clustering (optimizing for frequently used clusters)
and sharding.

If relationships are an important part of your data, looking into a Graph
database may be worth it.

### Key / Value

**Examples:** Redis, DynamoDB

Key / Value databases are fairly simple; you have a key in the database, and
that holds some value. Some databases allow for arbitrary properties within a
single key, and some others will hold whatever data you give them.

The fundamental units of data are:

- **Keys**, which identify a value uniquely, and
- **Values** or **Properties**, which depending on implementation allow for
  various types of data to be stored.

These can be useful for caching, and for holding less-structured unique data.

### Blob

**Examples:** s3, Blob Storage

Short for Binary Large OBject, Blob storage is the best place to keep files,
images, user documents, and other binary data. Generally, there aren't many
operations on blob storage because it acts like a file system where you can
upload and delete objects.

The fundamental unit of data are:

- **Objects**, normally some binary that is required to run your app (user
  profile pictures, shared documents, etc).

### In-Memory

**Examples:** Redis, ElastiCache

When read and write speed is very important (AWS claims microsecond read and
single-digit millisecond write latency), you want your data backend to not rely
on writing to a disk and live in-memory instead. Generally the tradeoff of this
is that the data is volatile; if the database shuts down for any reason, the
data is lost.

This can be mitigated by snapshots or storing a log of transactions, but will
vary by implementation. This is frequently combined with a Key / Value database,
but can apply to others.

### Flatfile

**Examples:** SQLite, H2

Sometimes it doesn't make sense to have a whole database running to run an
application, such as when running tests. Flatfile databases write to an
arbitrary file, which makes them easy to create and throw away.

Some apps are now using flatfile databases like SQLite in production, like
Turso. Sites like Notion also now use SQLite to speed up saved local data.
