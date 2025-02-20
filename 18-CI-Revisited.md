# Lecture 18 - CI Revisited

<!-- START doctoc generated TOC please keep comment here to allow auto update -->

- [Lecture 18 - CI Revisited](#lecture-18---ci-revisited)
  - [Links](#links)
  - [Events](#events)
  - [News](#news)
  - [Tooling](#tooling)
  - [Continuous Integration](#continuous-integration)
    - [GitHub Actions](#github-actions)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Links

- [Feedback Form](https://docs.google.com/forms/d/e/1FAIpQLSdcu-u0LD5kB9rhOcA7E1ZCw6w05RlejzrFrRALEz7krkLjVQ/viewform?usp=sf_link)
- [Q&A Form](https://docs.google.com/forms/d/e/1FAIpQLSd4c3JqKFSybays7xUNk3EeiUaDak7XvRqRyosng0ATCZf2bQ/viewform?usp=sf_link)
- [Office Hours](https://calendly.com/hhenrichsen)

## Events

- [HackUSU](https://huntsman.usu.edu/hackusu/) - February 28th - March 1st
- Student Ratings - should be opening soon. Please do them and be honest. The
  feedback is useful to me and, if positive, gives reason to hire me again.
- Q&A Day - next week. Please submit questions in advance if you think of them,
  and if you're interested in giving a tech talk let me know so I can get you on
  the docket.

## News

## Tooling

I think there are a collection of useful tools that any project should have, and
should have well-integrated with the development and deployment process. My
favorites in order are:

- Formatter
- Type Checker
- Linter
- Container / Executable Build

Many of these are super simple to set up in a project. Many languages even come
with formatters, linters, and type checkers; external tools for these are more
common in interpreted languages like JavaScript and Python. Some common ones
I've used are:

- [Prettier](https://prettier.io/) (formatter for JavaScript, JSON, HTML, CSS,
  and friends)
- [ESLint](https://eslint.org/) (linter for JavaScript)
- [TypeScript](https://www.typescriptlang.org/) (type checker for JavaScript)
- [Black](https://pypi.org/project/black/) (formatter for Python)
- [pylint](https://pypi.org/project/pylint/) (linter for Python)
- [ruff](https://github.com/astral-sh/ruff) (linter + formatter for Python)
- [gofmt](https://pkg.go.dev/cmd/gofmt) (formatter for Go)
- [rustfmt](https://github.com/rust-lang/rustfmt) (formatter for Rust)
- [clippy](https://doc.rust-lang.org/stable/clippy/index.html) (linter for Rust)
- [SpotBugs](https://spotbugs.github.io/) (linter for Java)
- [Checkstyle](https://github.com/checkstyle/checkstyle) (formatter for Java)

Your setup may vary, but for JavaScript this tends to be pretty simple. You
install the packages as dev dependencies:

```
npm i -D prettier eslint typescript
```

Then you add scripts to your package:

`package.json`

```json
{
  "name": "my-app",
  "scripts": {
    "lint": "eslint",
    "format": "prettier . --check",
    "lint:write": "eslint --fix",
    "format:write": "prettier . --write",
    "typecheck": "typescript --no-emit"
  }
}
```

You can also init eslint if you want to pick a configuration file and set
defaults. I normally configure Prettier, then disable any ESLint rules that
would conflict with Prettier. This is pretty easy:

```
npm i -D eslint-config-prettier
```

`.eslintrc.json`

```json
{
  "extends": {
	"prettier"
  }
}
```

Next.js comes with a configuration, and gives you the scripts to run this in
your project as well. They give you a convenient `npx next lint` command to use
to trigger linting, type checking, and formatting. If you install prettier, your
editor should pick it up and start formatting the files with it.

If your tools are well-integrated, you shouldn't have too many problems pushing
code that doesn't work. Your editor should space things out correctly on its
own, and should integrate warnings and errors from your tools. Depending on how
fast your tools are to run, it can be worth investing in pre-commit hooks to run
these tools as well; [husky](https://typicode.github.io/husky/) can be useful
for that if you're in the JavaScript ecosystem, as many of you are.

However, I don't think it's enough to have these tools configured at an
IDE-level. It's easy for user configuration to drift (or for new folks to join
with differently configured editors), for updates to wipe it away, or other
similar things. It's not worth code reviewing these things manually, since your
tooling should pick it up for you.

Instead, I think that these tools should be enforced at a repo level. This means
that there's a consistent configuration file, consistent scripts to run it on
both dev and CI machines, and it can catch issues before they make their way to
production. I think this is easiest done with CI.

## Continuous Integration

Continuous Integration is an idea centered around getting as much automated
feedback as quickly as possible for code that humans push. It originated around
testing (and is generally still used for that as its main focus), but the whole
idea of automated pipelines that run when code is pushed is useful for more than
testing.

We've talked about my preferences for testing before, but one thing I like to
emphasize is that continuous tests are the thing that should give you the
confidence to release your code automatically. I don't like releasing untested
code, so I tend to manually test my smaller projects. That time adds up, though,
and I'd rather write tests that will do that for me.

Once you have paying users, I think you have an obligation to have basic testing
at minimum. You should know if a deploy takes down your site. You should know if
a newly pushed branch _would_ take down your site when deployed. It's for this
reason that I split the assignments into two; one to get the pipeline in place,
and one to use that pipeline once you've given it the chance to mature a little
bit.

If formatting and linting really aren't concerns to you, I still recommend
making them build steps, but making them marked as optional. That way you can
know if code you're merging has potential issues, either logical- or
formatting-wise. The whole idea with these is that you should know what's going
on in your codebase without needing to do the manual work to understand every
step.

There are lots of people who will want your money to set up a pipeline for you.
I've seen lots of Jenkins thrown around, and that's indeed what we use at work.
It takes a group of engineers to keep it running and up to date, though, so I
find keeping it simple is a good idea. Most Git providers will have a built in
pipeline runner, like GitHub Actions.

### GitHub Actions

GitHub Actions are CI (and potentially CD) pipelines as code. They're pretty
simple, since they're mostly a list of commands to run. For example, here's one
that checks format, linting, and tests anytime a pull request is opened or
updated on the main branch:

`.github/workflows/verify.yaml`

```yaml
name: Verify

on:
  pull_request:
    branches:
      - main

jobs:
  verify:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      - name: Install Node.js
        uses: actions/setup-node@v2
        with:
          node-version: "20"

      - name: Install Dependencies
        run: npm install

      - name: Format Code
        run: npm run format

      - name: Lint Code
        run: npm run lint

      - name: Test Code
        run: npm run test
```

And I mentioned CD pipelines; if you're confident in your tests and checks, you
can run the build on GitHub before it tries to go to Vercel (or AWS, or any
other provider). You'll have to set up your repo environment variables, but
again this tends to be pretty straightforward:

`.github/workflows/preview.yaml`

```yaml
name: Vercel Preview Deployment

env:
  VERCEL_ORG_ID: ${{ secrets.VERCEL_ORG_ID }}
  VERCEL_PROJECT_ID: ${{ secrets.VERCEL_PROJECT_ID }}

on:
  pull_request:
    branches:
      - main

jobs:
  Deploy-Preview:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      - name: Install Vercel CLI
        run: npm install --global vercel@latest

      - name: Pull Vercel Environment Information
        run:
          vercel pull --yes --environment=preview --token=${{
          secrets.VERCEL_TOKEN }}

      - name: Build Project Artifacts
        run: vercel build --token=${{ secrets.VERCEL_TOKEN }}

      - name: Deploy Project Artifacts to Vercel
        run: vercel deploy --prebuilt --token=${{ secrets.VERCEL_TOKEN }}
```

You can swap this to production, too by replacing `--environment=preview` with
`--environment=production`, and adding `--prod` to both `build` and `deploy`
steps.

You can keep these as simple or as complex as you want. For example, here's one
that runs a local version of supabase:

```yaml
name: Verify (Preview)

on:
  push:
    branches: [develop]
  pull_request:
    branches: [main, develop]

jobs:
  build:
    name: Build and Run Tests (Preview)
    runs-on: ubuntu-latest
    - uses: actions/checkout@v3

    - uses: supabase/setup-cli@v1
      with:
        version: latest

    - name: Install Node.js
      uses: actions/setup-node@v3
      with:
        node-version: 18

    - name: Install dependencies
      run: npm install

	- name: Lint
      run: npm run lint

    - name: Check Format
      run: npm run format

    - name: Start Supabase API and Database
      # exclude these services that I'm not using to make things faster
      run: supabase start -x realtime,storage-api,imgproxy,inbucket,edge-runtime,logflare,vector,supavisor

    - name: Remap Supabase Environment Variables
      # `grep NAME` grabs the variable NAME from the supabase env
      # `cut -d "=" -f2 <<<` grabs the thing after the equals from the string provided to it
      # `echo "NAME=$(...)" >> $GITHUB_ENV` stores the result in the NAME variable
      # I have 4 variables I'm interested in from this project.
      run: |
        echo "NEXT_PUBLIC_SUPABASE_ANON_KEY=$(cut -d "=" -f2 <<< "$(supabase status -o env | grep ANON_KEY)")" >> $GITHUB_ENV
        echo "NEXT_PUBLIC_SUPABASE_URL=$(cut -d "=" -f2 <<< "$(supabase status -o env | grep API_URL)")" >> $GITHUB_ENV
        echo "SUPABASE_SERVICE_ROLE_KEY=$(cut -d "=" -f2 <<< "$(supabase status -o env | grep SERVICE_ROLE_KEY)")" >> $GITHUB_ENV
        echo "DATABASE_URL=$(cut -d "=" -f2 <<< "$(supabase status -o env | grep DB_URL)")" >> $GITHUB_ENV

    - name: Build
      # Next wants the environment variables available at this point. Make that so.
      run: npm run build

    - name: Run unit tests
      run: npm run test

```

You can even automate branches and such from here; you can write an action that
runs the tests on your `main` branch, and creates new `main-built` and
`main-stable` branches, or even pushes to other repositories. I think learning
how to use these effectively can speed you up massively and keep you focused on
developing features rather than chasing bugs or tweaking formatting manually.
