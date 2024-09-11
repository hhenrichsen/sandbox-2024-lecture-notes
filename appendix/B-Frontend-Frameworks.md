# Frontend Frameworks

_Frameworks are how you build webpages that respond to outside data and user
input. Many of the frontend frameworks are now moving towards SSR which allows
you to also use server information and generate the page even if the user is not
using JavaScript, depending on the implementation. There are so many of these._

## Frameworks

### The Original Ones

- **[Vanilla JS](https://vanilla.js.org/)**–The web has become much more
  cross-compatible nowadays than it was 5 or 10 years ago. The JavaScript built
  into each browser is now more than enough to build pages that respond to
  changes, whether user-initiated or remote.
- **[jQuery](https://jquery.com/)**–Before the web had good, cross-browser
  support for DOM manipulation and AJAX requests, jQuery was the way to do these
  things. It's still used on some sites, so is worth mentioning.

### The Popular Ones

- **[React](https://react.dev/)**–React is one of the most popular frameworks
  currently. My theory for why it's more popular than Vue or Angular is that
  there are many ways to do things in React, and it's fairly un0pinionated in
  how you set things up. For example, in Next.js, there are 4 different ways to
  load CSS! This lets you use what makes sense to you in your codebase.
- **[Vue](https://vuejs.org/)**–Vue is a bit more opinionated than React, and
  I've found that it's a good entrypoint to web frameworks if you've never
  worked in one before. [Nuxt](https://nuxt.com/) is Vue's counterpart to
  Next.js.
- **[Angular](https://angular.io/)**–Angular is one of the most opinionated
  frameworks out there. Angular has concepts like structural directives: if you
  don't like the way that `*ngIf` works, write your own. It enforces separation
  of files between HTML, CSS, and JS. It supports SSR via
  [Angular Universal](https://angular.io/guide/universal). I have found than
  Angular works best for larger codebases where standards are required; Angular
  includes many of them that make sense, where in Vue or React you would have to
  write, set, and enforce standards on your own. That said, that overhead
  doesn't make as much sense outside of an environment with many engineers where
  those standards are needed.

### The New Ones

- **[Svelte](https://svelte.dev/)**–Svelte is a newer framework that has taken
  some of the learnings from React, Vue, and Angular. It has a lot of cool built
  in utilities.
- **[HTMX](https://htmx.org/)**–HTMX adds a bunch of utilities to the HTML of a
  page, rather than using JS to write the HTML. It expands the available REST
  verbs, allows using websockets, and facilitating animations without writing
  any JS on the client.
- **[Qwik](https://qwik.builder.io/docs/)**–Qwik is built around only loading
  what's needed first, allowing you to get pages that paint and become
  interactive easily. Instead of hydration (downloading and executing component
  code for the current page), Qwik markets that it "resumes" the page instead,
  only loading what's required when the user asks for it.
- **[Lit](https://lit.dev/)**–Built on WebComponents, Lit is a framework that
  helps you use WebComponents to build sites without much overhead.
- **[Alpine](https://alpinejs.dev/)**–A small, lightweight alternative to larger
  frameworks like Angular, React, and Vue.
- [SolidJS](https://www.solidjs.com/)-A modern framework designed to build
  responsive, performant UIs. Claims to be almost as fast as Vanilla.

## Component Libraries

_Component libraries are the primitives used to build an app, and generally run
on top of a framework. I recommend using one, since most of them come with
automatic accessibility benefits, as well as save you time reinventing the
wheel._

- **[Radix](https://www.radix-ui.com/)**–A set of primitive components that can
  be used to build most user interfaces.
- **[Shadcn](https://ui.shadcn.com/)**–Radix with Tailwind, and more. Instead of
  installing their components as packages, they add the components to your
  project for you to use and edit as you like. I'm pretty fond of that approach.
- **[Material](https://mui.com/material-ui/)**–Most of you have seen this
  component library. It's the one that drives the Google design system, and is
  used in many apps even beyond Google's own apps.
- **[WebComponents](https://www.webcomponents.org/introduction)**–WebComponents
  are really cool, since they work without a framework and allow for building
  reusable components without needing the overhead of a framework. I think this
  is worth mentioning, although these are a bit tricky to get working.
- **[DaisyUI](https://daisyui.com/)**–A library built on top of tailwind that
  works to also reduce the number of classes one must stack to get a desired
  result.

## CSS Frameworks

- **[Tailwind](https://tailwindcss.com/)**–Tailwind is an interesting tool that
  allows you to quickly make changes to the style of components without needing
  to change a CSS file. It makes a lot more sense if you understand what CSS the
  classes are going to be writing, but it's a useful tool if you want to quickly
  prototype things. I find that it also makes my HTML code way harder to
  understand at a glance (and can bloat the size of the document sent to users
  if I'm not careful), but the CSS can be hidden into classes and such to help
  alleviate that once I'm done prototyping.
- **[UnoCSS](https://unocss.dev/)**–A faster implementation of Tailwind with
  some extra features like
  [Attributify Mode](https://unocss.dev/presets/attributify#attributify-mode)
  which can deal with some of the readability concerns of Tailwind, to a degree.
- **[PicoCSS](https://picocss.com/)**–A tiny CSS framework that comes with a
  bunch of modern default styles for builtin components, rather than utilities
  to write your own styles.
- **[Open Props](https://open-props.style/)**–Similar to Tailwind, but instead
  using CSS variables that can be mixed in to an existing CSS file.
- **[PureCSS](https://purecss.io/start/)**–Another small collection of CSS
  utilities for common things like layouts, forms, and buttons.
- **[Bootstrap](https://getbootstrap.com/)**–Most of your have seen this CSS
  library before. Lots of websites have used Bootstrap to get a base set of
  styles, such as for menus, buttons, and layouts.
- **[Bulma](https://bulma.io/)**–Sometimes called "modern bootstrap", a
  collection of CSS utility classes to help you with some of the fundamentals of
  a website.

## Bundlers and More

### Bundlers

- **[Webpack](https://webpack.js.org/)** – Webpack is one of the original
  bundlers. It tends to require a lot of setup to get right, but some other
  packages (like Next.js) have started shipping with reasonable defaults for
  Webpack. This is why when configuring Tailwind, some files are required to
  stay as `.js` files, but some can be `.ts` files; webpack has an expectation.
- **[Rollup](https://rollupjs.org/)** – Rollup is the bundler behind Vite, and
  has much more reasonable defaults. I have added an empty rollup config to some
  of my other projects, and had it just work. It's a bit more modern and usable
  than Webpack, I've found.

### Other Tools

- **[Speedy Web Compiler](https://swc.rs/)** – can take the place of Babel and
  some other tools, depending on your needs. Implemented in Rust, so can be much
  faster than other JavaScript tools.

### Build Steps

- **[Babel](https://babeljs.io/)** – Babel allows you to compile your JavaScript
  into JavaScript, with additional rules. Using some of the below tools, this
  means dealing with differences between browsers, supporting new features, or
  allowing special types of syntax (like decorators). Babel can be its own
  bundler, but is generally a step in another bundler's toolchain.
- **Browserlist / Polyfills** – Polyfills allow you to support newer JavaScript
  features on older browsers by adding implementations of those features to your
  runtime environment.
- **Minification / Uglification** -- One way to speed up your website is to
  reduce the amount of data that a user has to download. Minification strips
  whitespace, variable names, class names, etc. and turns them into as small a
  file as possible. It also makes it very hard to read or debug, so this is
  generally only done for production.
