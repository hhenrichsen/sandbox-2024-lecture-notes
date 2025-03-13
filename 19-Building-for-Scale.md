# Lecture 19 - Building for Scale

<!-- START doctoc generated TOC please keep comment here to allow auto update -->

- [Lecture 19 - Building for Scale](#lecture-19---building-for-scale)
  - [Performance Monitoring and Profiling](#performance-monitoring-and-profiling)
  - [Vertical Scale](#vertical-scale)
  - [Horizontal Scale](#horizontal-scale)
    - [Load Balancers](#load-balancers)
    - [Queues](#queues)
  - [Kubernetes](#kubernetes)
  - [Additional Reading](#additional-reading)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Performance Monitoring and Profiling

Premature optimization is dangerous because it can turn into wasted effort
incredibly easily, and even if the optimization works, you are introducing extra
complexity. You could even be optimizing something that doesn't make a
worthwhile impact on performance.

At minimum, I think you should profile whatever you think is performing poorly
so you can identify the best place to spend your time.

However, that gets harder as you get more services and users. This is where
performance monitoring and logging come in. I've mentioned observability before;
ideally you should know what's going on on your servers, even when you're not
actively looking at them.

Software like [Grafana](https://grafana.com/oss/grafana/) and
[Prometheus](https://prometheus.io/) can be useful for this. Once you have
servers that are available on the internet, you probably want alerts as well,
for things like the root user logging in or the server going down.

Google has a few books that are useful for learning about engineering operations
and site reliability engineering. I think there's a whole class worth of
material here, but the sections on investigating code, disaster planning, and
crisis management are worth a read at a minimum. The books can be found online
here:

- [Building Secure and Reliable Systems](https://google.github.io/building-secure-and-reliable-systems/raw/toc.html)
- [Site Reliability Workbook](https://sre.google/workbook/table-of-contents/)
- [Site Reliability Engineering](https://sre.google/sre-book/table-of-contents/)

I think Scale is at its core a reliability issue, so understanding the ways that
larger organizations do it can be a good way to learn what the ceiling for these
kind of things are, and can help you learn how to deal with the problems that
led those organizations to develop those strategies in the first place.

## Vertical Scale

Generally, the easiest way to scale a server that's under demand is to give it
more resources: memory, CPU cores, faster processors, or larger disks. This is
the easiest option, especially when you're just starting out. You just pay for a
bigger, faster server.

## Horizontal Scale

This is the next step when you're no longer able to scale vertically; then, the
best option is
[a few servers and a load balancer](https://shayy.org/posts/a-few-servers). Once
you can't add more compute to individual instances, you need multiple instances.

Serverless and edge computing are the PaaS version of these, but those tend to
have a lot of overhead, both in terms of compute time and cost.

Unlike vertical scale, horizonal scale introduces a lot more problems to deal
with. You'll have to make sure that your endpoints don't depend too much on each
other using memory, since a different process or server can handle each request.
It also can make debugging more difficult, since there are more steps between
the user getting to your servers.

The benefit here, on top of increased ability to deal with user demand, is
increased redundancy. If one server crashes, your other servers can likely deal
with the demand.

### Load Balancers

To scale horizontally, you need to distribute traffic between many servers.
There are a bunch of prebuilt ones available:

- Caddy
- Nginx
- Apache
- HAProxy

Each one has their quirks and levels of maturity. I've heard about Caddy being
used fairly frequently among sandboxers. Some of the other ones are more mature
and battle-tested, but I would hope that by the time that matters, you'll have a
couple engineers on your team to help you figure out migrating to (or building
your own) other options.

### Queues

Depending on your situation, you might not need to deal with more load, but with
more work and processing to do. In a case like this, it can make sense to split
work between a number of worker services. To do this, the simplest way is to add
a queue and have workers pull from that. This is normally done behind the
scenes, with a way for users to check up on how their work is going.

Queue length is a good metric here to measure reliability and load. If your
queue is consistently longer than your workers can get through, it might be time
to add another worker or two.

Depending on how you set these up, these can also be used in conjunction with
webhooks to make sure that you're not timing out on hosting providers where
that's a concern, like lambdas or Vercel. Your main server just pushes stuff to
the queue, and an endpoint gets hit when the work is done, whenever that
happens. This is pretty common for things like image processing, transcription,
and other similar things.

## Kubernetes

(or minikube, or k3s if you want to try it out without needing to build a full
cluster)

Kubernetes is something I hear about a lot when I hear about scale. What it
allows is setting up a fixed (or semi-fixed) pool of compute resources and
allowing services to scale up and down within those based on traffic and other
similar things.

## Additional Reading

- **Read:**
  [Fernando Villalba: It's not Microservice or Monolith](https://fernandovillalba.substack.com/p/its-not-microservice-or-monolith)
  – this is a good wrap-up article about the general decision for Microservices
  or Monolith.
- **Read:**
  [AWS: Database Sharding](https://aws.amazon.com/what-is/database-sharding/) or
  [MongoDB: Database Sharding](https://www.mongodb.com/features/database-sharding-explained)
- **Skim:**
  [IBM: What is Data Replication](https://www.ibm.com/topics/data-replication)
