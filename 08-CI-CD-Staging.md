# Lecture 08 - CI, CD, Staging Environments

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [Lecture 08 - CI, CD, Staging Environments](#lecture-08---ci-cd-staging-environments)
  - [Links](#links)
  - [Events and Announcements](#events-and-announcements)
  - [News](#news)
  - [Follow-Up from Last Time](#follow-up-from-last-time)
  - [Continuous Integration](#continuous-integration)
    - [Automated Tests](#automated-tests)
    - [Incremental Changes](#incremental-changes)
    - [Code Reviews](#code-reviews)
    - [GitHub Actions](#github-actions)
  - [Continuous Delivery](#continuous-delivery)
  - [Staging Deploys](#staging-deploys)
  - [Licensing](#licensing)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Links

- [Feedback Form](https://docs.google.com/forms/d/e/1FAIpQLSdcu-u0LD5kB9rhOcA7E1ZCw6w05RlejzrFrRALEz7krkLjVQ/viewform?usp=sf_link)
- [Q&A Form](https://docs.google.com/forms/d/e/1FAIpQLSd4c3JqKFSybays7xUNk3EeiUaDak7XvRqRyosng0ATCZf2bQ/viewform?usp=sf_link)
- [Office Hours](https://calendly.com/hhenrichsen)

## Events and Announcements

_I mostly know about Lucid events, but feel free to slack me a day or two before
class and I can get things added here._

## News

- Flutter fork [Flock](https://flutterfoundation.dev/) announced. Right now it's
  just a 1:1 copy of Flutter, but it says that it will add new community
  features.
- [Svelte 5](https://svelte.dev/blog/svelte-5-is-alive) has been released.
- [Oasis](https://oasis-model.github.io/), an AI model trained on Minecraft
  footage, has been released.

## Follow-Up from Last Time

## Continuous Integration

CI is short for Continuous Integration. There are a couple facets to this:

- Fast turnaround time on if the code on a branch is working properly (builds,
  formats, tests)
- Code reviews
- Incremental changes rather than massive merges

### Automated Tests

I feel like I have beat this horse into the ground. Write tests. They're a
smaller up-front investment for a longer-term benefit of not being able to break
things as easily.

### Incremental Changes

Not every issue needs to be solved in one pull request. If you can break things
up into smaller changes, both you and your collaborators will have a better
time. Smaller changes are easier to review, easier to write tests for, and
easier to merge. There's a balance to find here between PRs and commits as well,
but incremental changes are something worth considering as you work with other
engineers.

### Code Reviews

Unless you are working alone (and even then, I'm happy to review PRs if you
want), you should have another human review your code as well. It's a fantastic
way to make sure that your code can be understood, and fits in both of your
mental models of your larger system.

### GitHub Actions

GitHub actions is a CI pipeline, and generally good for running automation on
pull requests without needing to set up other automation. Other git providers
have similar pipelines as well, or you can use a third party pipeline like
Jenkins.

Here is the workflow I am using for my project. It runs with a database, and
runs my unit tests, formatter, linting, etc. to make sure that it's deployed.

`.github/workflows/tests.yml`

```yaml
name: Build and Deploy (Preview)

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

## Continuous Delivery

Continuous delivery means automatically releasing your code to your users. Some
teams find this fine to do as soon as it is merged and tested. If your app is
much more client-focused (and heavy), it might make sense to keep it cached, and
release once or twice a day to reduce the amount of bandwidth users need to use
loading the code.

On Vercel, this is automatic. When you push to your main branch, it gets
deployed to production so long as the build passes. I found that the double
build isn't as useful, so I deploy as a part of my GitHub Actions build process
[here](https://github.com/hhenrichsen/point-control-next/blob/next/.github/workflows/tests.yml#L104),
but you can do what works best for you.

Some other options:

- Build a container on your CI pipeline, deploy it to a container registry (like
  Google Cloud, or AWS), and trigger a deploy from the container registry.
- SSH into your production server, trigger a git pull, and restart the server.

## Staging Deploys

I've mentioned this in the past; having a safe environment to test changes that
mirrors your production environment can save you a lot of headache in the
future. I recommend a workflow that looks like this:

- Create a pull request to a `next` or `develop` branch that runs tests, format,
  etc.
- Do code review on the pull request
- Merge to `next` or `develop`
- Automatically deploy that to your staging environment
- Check the logs for database / build issues (or better yet, create alerts for
  those things that will notify you if things fail)
- Run tests against that environment (optional)
- Check that your flow works in that environment
- Merge to `main`

## Licensing

Generally with Licensing, you want a system that can do a couple of things:

- Know what license each user or account has
- Know what features each license is allowed to use
- Know what features each user is allowed to use (normally by proxy to the
  license)

One way you might model this is like this:

```ts
export class Feature {
  public static readonly ImageUpload = new Feature(
    "Image Upload",
    "Upload images to your account."
  );

  public static readonly CreateNotes = new Feature(
    "Create Notes",
    "Create notes and store them in your account."
  );

  public static readonly AICompose = new Feature(
    "AI Compose",
    "Compose notes using AI."
  );

  constructor(public name: string, public description: string) {}
}

export class License {
  static readonly Free = new License(0, "Free", "", [], 100);
  static readonly Plus = new License(
    1,
    "Plus",
    "prod_RAbmvCKZ3FEpJD",
    [(Feature.ImageUpload, Feature.CreateNotes)],
    10000
  );

  static readonly ById = {
    [License.Free.id]: License.Free,
    [License.Plus.id]: License.Plus,
  };

  static readonly ByProductId = {
    [License.Plus.productId]: License.Plus,
  };

  constructor(
    public readonly id: number,
    public readonly name: string,
    public readonly productId: string,
    public readonly features: Feature[],
    public readonly storage: number
  ) {}
}
```

And then in my database:

```sql
CREATE TABLE public.user_payments (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id UUID REFERENCES auth.users NOT NULL,
    license_id INTEGER,
    payment_id UUID NOT NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT now(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT now()
);

ALTER TABLE public.user_payments ENABLE ROW LEVEL SECURITY;

CREATE POLICY "User can read notes."
ON public.user_payments FOR SELECT
TO authenticated
USING (auth.uid() = user_id);

UPDATE TABLE public.users ADD COLUMN license_id INTEGER;
```

The `users` table has a license_id to keep track of their current license, and
then the payments table stores their payment history.
