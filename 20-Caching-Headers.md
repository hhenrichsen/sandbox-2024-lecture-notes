# Lecture 20 - Caching, CDNs, and Headers

<!-- START doctoc generated TOC please keep comment here to allow auto update -->

- [Lecture 20 - Caching, CDNs, and Headers](#lecture-20---caching-cdns-and-headers)
  - [Links](#links)
  - [Events](#events)
  - [News](#news)
  - [Tech Talk](#tech-talk)
  - [Caching](#caching)
  - [Headers](#headers)
    - [Cache Control](#cache-control)
    - [Entity Tag](#entity-tag)
    - [Server Timing](#server-timing)
  - [CDNs and Object Storage](#cdns-and-object-storage)
  - [Service Workers](#service-workers)
  - [Chunking](#chunking)
    - [Critical CSS](#critical-css)
  - [Minification and Compression](#minification-and-compression)
    - [Minification](#minification)
    - [Compression](#compression)
  - [Prefetching](#prefetching)
    - [Preload Links](#preload-links)
    - [DNS Prefetch](#dns-prefetch)
  - [Images and Video](#images-and-video)
    - [Image Processing](#image-processing)
    - [Loading Large Content](#loading-large-content)
  - [Additional Reading](#additional-reading)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Links

- [Feedback Form](https://docs.google.com/forms/d/e/1FAIpQLSdcu-u0LD5kB9rhOcA7E1ZCw6w05RlejzrFrRALEz7krkLjVQ/viewform?usp=sf_link)
- [Q&A Form](https://docs.google.com/forms/d/e/1FAIpQLSd4c3JqKFSybays7xUNk3EeiUaDak7XvRqRyosng0ATCZf2bQ/viewform?usp=sf_link)
- [Office Hours](https://calendly.com/hhenrichsen)

## Events

- Q&A Day - next week. Please submit questions in advance if you think of them,
  and if you're interested in giving a tech talk let me know so I can get you on
  the docket.

## News

## Tech Talk

## Caching

One of the easiest ways to improve (CPU) performance is caching things. Most
apps include data that is difficult or time consuming to contain, so an easy way
to improve the performance is by saving the values and using those for an
appropriate amount of time.

This has a cost in either disk or memory space (depending on how you're caching
things and how long term they can be cached for), so it's something to keep in
mind if you take an approach like this that expands across your codebase.

You can cache across a multitude of layers. Libraries like Jotai can help you
build a cache-invalidate layer on top of primitives, like data sent over the
network, and how users modify those changes. Caches on the backend either will
look like static rendering, or the types of caching you see Redis or other
databases where values are saved in a volatile fashion.

Caching is most useful for sites where there's more fixed content than dynamism.
Things like blogs, store pages, and directories are easy to cache since the bulk
of the content doesn't tend to change based on the current user.

## Headers

### Cache Control

One of the simplest ways of getting performance gains on successive loads of
pages is to use the built-in caching of the browser via
[`Cache-Control`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Cache-Control)
headers. These can use a couple different ways of caching, including just asking
the server when that resource was last modified, and using the old value if it
hasn't been modified yet, or taking a duration and using the old value until
that time elapses.

If you're still serving your static resources from the same server as your API
and frontend, these can cut down on load times for an assortment of pages and
resource types. However, it's worth being mindful of initial load times without
any scripts or resources cached.

Cheatsheet: https://shayy.org/posts/cache-control

### Entity Tag

Another part of caching is entity tags. The
[`ETag`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/ETag)
header lets you create a cache key for a resource which clients can then store
and send with their next request. When requesting a resource, the tag can be
sent with
[`If-None-Match`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/If-None-Match)
header to compare the cache keys; if they're the same, the server can send a 304
response and reuse the cached version.

### Server Timing

Just like the devtools can read timings from the request, like the round trip
time, you can also add additional timing information into your headers using the
[`Server-Timing`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Server-Timing)
header.

## CDNs and Object Storage

CDNs, or content delivery networks, are super useful for static assets for
things like compiled JavaScript, stylesheets, HTML, images, and such. Common
providers include Cloudflare, Akamai, and AWS CloudFront. These improve
performance by serving static files closer to the user, and aggressively caching
them. Generally, resources in these work best with long cache cycles. When
combined with chunked JavaScript, certain files can remain cached for weeks,
months, or years, depending on how frequently they change.

## Service Workers

Service Workers are essentially a background process for your webpage. The main
use for these are caching, although you can push work to the service worker and
allow it to run without blocking the main thread of your app. These are
effectively installed locally, meaning that you can have partial or even full
offline support using a service worker.

These can listen to requests and respond before they even go to the network,
allowing them to read from a local cache. This can be yet another layer in a
cohesive caching strategy.

## Chunking

Chunking is splitting your clientside code up into smaller pieces that can be
cached and reused across pages, which can help subsequent load times when people
move across pages. This can be done via dynamic imports (where you split code
manually and trigger their loading logically), or via a bundler that finds the
imports and libraries used across pages and splits them into minimal pieces that
can reduce the total size of the JavaScript on the page by limiting the code
downloaded to what's needed to run the individual page.

### Critical CSS

Doing another request to load CSS after you load the page content can take
awhile, and can lead to content reflowing as the page loads. That's not ideal.

Instead, you can move the first-paint, layout-important CSS (also called
before-the-fold CSS) _before_ the content of the page, in the head (or body) of
your app. This is called
[Critical CSS](https://web.dev/articles/extract-critical-css).

[Inlining CSS is also really effective](https://strikingloo.github.io/inlining-css),
and can be an interesting experiment depending on how you're building your site.

## Minification and Compression

The speed at which the user gets access to your site is related to their
internet speed and the total size of your site. You can't control your user's
internet speed, but you can control the size of your site (especially the
initial load of your site). Minification and compression are two ways to do
this.

### Minification

The code your app builds for development is much larger than the code that needs
to be built for production. To the user's browser, variable names, inheritance
structures, function names, and imports are all pretty useless.

This is where minification can be really useful. Minification will take the code
of your site and reduce the names and structure as much as possible, stripping
out comments and mangling names to make it concise. Some will even do dead code
elimination and tree shaking, removing code that's never used.

Some framework production builds will do this for you. For others, you'll have
to set this up yourself.

### Compression

If you're building your server more from scratch, I think you should make sure
that your site supports compression. Your server can be set up to serve
compressed content, which will reduce traffic over the network and can increase
the speed at which your site loads.

Generally, the user should send headers that include
[`Accept-Encoding`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Accept-Encoding)
that tell you which kinds of compression they support. Some common algorithms
are `gzip` and `brotli`

## Prefetching

If you're not able to CDN cache things (and even when you are), it takes much
longer to read over the network than it does to read from disk. You can cut down
on this time by prefetching things. This doesn't need to be complicated
(although the more aggressively you do this, the more complicated it will get).

At it's simplest, you can let the page know to start loading certain things
before they're actually requested in the content of the page. For assets like
logos, fonts, and images, this can be really simple and make a decent impact.

### Preload Links

The simplest version of this is to create a link element, and preload some other
content. That way when your page requests it (like later, in the HTML), the
process has already started and the page can just wait for that to finish,
potentially saving you seconds on poor connections.

```html
<link
  rel="preload"
  href="https://images.my.site/logo.svg?ver=1258324"
  as="image"
  type="image/svg+xml"
/>
```

### DNS Prefetch

On multi-domain sites, the DNS resolution can also lead to delays. You can do
something similar to images, but instead ask to just resolve the DNS for a
certain domain so that when you make subsequent requests, you've already
resolved the domain and are ready to go.

```html
<link rel="dns-prefetch" href="//api.my.site/" />
```

## Images and Video

### Image Processing

For user uploaded images, you should be processing and limiting your images.
Most sites don't need megapixel images, especially for a 64x64 avatar that
you're going to see in the top right corner and nowhere else. That means you
shouldn't be serving or storing images at sizes larger than you're going to be
using them.

### Loading Large Content

Images and Videos are what make Web 2.0 what it is. The problem is that while
modern networks can support those, they're still really large files. Ideally,
when serving and loading this content, it should load lazily so you don't end up
incurring additional bandwidth costs.

## Additional Reading

- [Google Web.Dev "Fast" Category](https://web.dev/explore/fast)
