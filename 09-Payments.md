# Lecture 09 - Payments, Webhooks, and Revisiting Design

<!-- START doctoc generated TOC please keep comment here to allow auto update -->

- [Lecture 09 - Payments, Webhooks, and Revisiting Design](#lecture-09---payments-webhooks-and-revisiting-design)
  - [Links](#links)
  - [Payments](#payments)
    - [Setting up a Product Page](#setting-up-a-product-page)
      - [Dealing with Stripe](#dealing-with-stripe)
    - [The Handlers](#the-handlers)
    - [Setting up a Stripe Webhook](#setting-up-a-stripe-webhook)
      - [Some Pre-Work](#some-pre-work)
      - [The Webhook](#the-webhook)
    - [Listening](#listening)
  - [Notes on Design](#notes-on-design)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Links

- [Feedback Form](https://docs.google.com/forms/d/e/1FAIpQLSdcu-u0LD5kB9rhOcA7E1ZCw6w05RlejzrFrRALEz7krkLjVQ/viewform?usp=sf_link)
- [Q&A Form](https://docs.google.com/forms/d/e/1FAIpQLSd4c3JqKFSybays7xUNk3EeiUaDak7XvRqRyosng0ATCZf2bQ/viewform?usp=sf_link)
- [Office Hours](https://calendly.com/hhenrichsen)

## Payments

Generally, you don't need to be complex at the beginning of an app. I've had
teams using Venmo, or using Stripe's No Code options, or other things like that.
However, at some point it makes sense to add self-serve options for customers so
they don't have to contact you to purchase your product.

Like I usually say, learn at the lowest cost center. I'd apply that here to say
"build what makes sense at the scale you're at". This is one of the more complex
demos that I've had to put together.

This demo looks a little different from Stripe's example because it's more "real
world", in that I have users signed in and database state, where Stripe's
tutorial is more about how to get the payment done at all.

### Setting up a Product Page

_If you prefer, this is also available in a repo
[here](https://github.com/hhenrichsen/hexnotes/tree/lecture-09)._

High level, I'm working with a structure like this:

```
app
├───(features)                 -- a grouping folder used to organize features
│   └───pricing                -- pages related to pricing
│       ├───success
│       │   └───page.tsx       -- the page someone lands on after checking out
│       ├───getproducts.ts     -- a helper for listing Stripe products
│       ├───page.tsx           -- the product listing page
│       └───productlisting.tsx -- client component for the listing page
└───api
    ├───checkout
    │   ├───checkout-session
    │   │   └───route.ts       -- used to trigger buying a product on Stripe
    │   └───portal-session
    │       └───route.ts       -- used to open subscription management on Stripe
    └───webhooks
        └───stripe
            └───route.ts       -- used to handle information from Stripe
utils
├───stripe
│   └───stripe.ts              -- holds my stripe client global variable
├───supabase
│   ├───client.ts              -- my supabase clientside client
│   ├───middleware.ts          -- sets supabase cookies and redirects anon users
│   ├───server.ts              -- my supabase serverside client
│   └───service.ts             -- my supabase service client
└───user
    └───license
        ├───feature.ts         -- feature class, from last lecture
        ├───license.ts         -- license class, from last lecture
        ├───updatelicense.ts   -- a query to update a user's license
        └───withfeature.ts     -- checks a user's license against a feature
```

Let's start by installing some packages:

```
npm i stripe server-only
```

We'll be using Stripe for what's hopefully obvious, and server-only to help us
poison any modules that we try to include in client code (which should help us
catch importing those in the wrong place).

I'm also going to add some fields to my `.env` file. The first is used to
assemble full URLs for redirects, the next two of these can be grabbed from your
stripe dashboard, and the last one is because I'm using supabase as my backend,
and I'm going to want to do some system actions when we get events from Stripe.

`.env.example`

```diff
  NEXT_PUBLIC_SUPABASE_URL=your-project-url
  NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
+ NEXT_PUBLIC_BASE_URL=http://localhost:3000
+ STRIPE_WEBHOOK_SECRET=
+ STRIPE_SECRET_KEY=
+ SUPABASE_SERVICE_ROLE_KEY=
```

I'm going to want some helper functions to get me started and interact with
Stripe:

`utils/stripe/stripe.ts`

```ts
import "server-only";
import { Stripe } from "stripe";

const { STRIPE_SECRET_KEY } = process.env;
if (!STRIPE_SECRET_KEY) {
  throw new Error("STRIPE_SECRET_KEY was not provided");
}

export const stripeClient = new Stripe(STRIPE_SECRET_KEY);
```

I also want a service client to supabase:

`utils/supabase/service.ts`

```ts
import "server-only";
import { createClient } from "@supabase/supabase-js";

export const createServiceClient = async () =>
  await createClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.SUPABASE_SERVICE_ROLE_KEY!,
    {
      auth: {
        persistSession: false,
        autoRefreshToken: false,
        detectSessionInUrl: false,
      },
    }
  );
```

I also want my business logic for updating a license in my database:

`utils/user/license/updatelicense.ts`

```ts
import "server-only";
import { createServiceClient } from "@/utils/supabase/service";

/**
 * @returns success
 */
export async function updateLicense(
  email: string,
  licenseId: number,
  pending = false
) {
  const client = await createServiceClient();

  const { error } = await client
    .from("public.users")
    .update({ license_id: licenseId, payment_pending: pending })
    .eq("email", email);

  if (error) {
    console.error(
      `Failed to update license due to error: ${error} ${error.message}`
    );
    return false;
  }

  return true;
}
```

#### Dealing with Stripe

I'm going to want a component to list the available plans. I'll keep it simple
to something like this:

`app/(features)/pricing/productlisting.tsx`

```tsx
"use client";

export function ProductListing({
  id,
  name,
  description,
  priceId,
  price,
}: {
  id: string;
  name: string;
  description: string;
  priceId: string;
  price: string;
}) {
  return (
    <form method="POST" action="/api/checkout/checkout-session">
      <input type="hidden" name="id" value={id} />
      <input type="hidden" name="priceId" value={priceId} />
      <button
        role="submit"
        className="max-w-prose border-white rounded-md border-2 p-2"
      >
        <h2>{name}</h2>
        <p>{description}</p>
        <p>{price}</p>
      </button>
    </form>
  );
}
```

Next, I need some helpers to map my licenses to Stripe products:

`app/(features)/pricing/getproducts.ts`

```ts
"use server";

import { stripeClient } from "@/utils/stripe/stripe";
import type { Stripe } from "stripe";
import { License } from "@/utils/user/license/license";

export const getProducts = async () => {
  console.log("Cache miss on products");
  // Get the list of licenses
  const licenses = Object.values(License.ByProductId);

  // Map those to Stripe products
  const products = await Promise.all(
    licenses.map((license) => stripeClient.products.retrieve(license.productId))
  );

  // Get prices for each of them and filter out any missing products
  const productsWithPrices = (
    await Promise.all(
      products.map(async (product) => {
        if (typeof product.default_price != "string") {
          return;
        }
        const price = await stripeClient.prices.retrieve(product.default_price);

        return {
          ...product,
          price: price as Stripe.Price,
        };
      })
    )
  ).filter((product) => !!product);

  // Map those to keys and fix up data as needed
  const productInfo = productsWithPrices.map(
    (
      product
    ): {
      price: string;
      priceId: string;
      lookupKey: string | undefined;
      name: string;
      description: string;
      id: string;
    } => ({
      price: product.price.unit_amount
        ? product.price.unit_amount / 100 + " " + product.price.currency
        : "Invalid price",
      priceId: product.price.id,
      lookupKey: product.price.lookup_key ?? undefined,
      name: product.name,
      description: product.description ?? "",
      id: product.id,
    })
  );

  return productInfo;
};
```

Now, I'll create a page to hold my pricing information:

`app/(features)/pricing/page.tsx`

```tsx
import { getProducts } from "./getproducts";
import { ProductListing } from "./productlisting";
import { createClient } from "@/utils/supabase/server";

export default async function Page(): Promise<JSX.Element> {
  const client = createClient();
  const user = await client.auth.getUser();
  // Check for a license; if the user has one, they can manage their sub
  const hasLicense = !!(
    user.data.user &&
    (
      await client
        .from("profiles")
        .select("license_id")
        .eq("id", user.data.user.id)
        .single()
    ).data?.license_id
  );

  const products = await getProducts();

  return (
    <section>
      {products.map(({ id, name, description, price, priceId }) => (
        <ProductListing
          key={id}
          id={id}
          priceId={priceId}
          name={name}
          description={description}
          price={price}
        />
      ))}
      {!hasLicense ? null : (
        <form method="POST" action="/api/checkout/portal-session">
          <button role="submit">Manage Subscription</button>
        </form>
      )}
    </section>
  );
}
```

### The Handlers

These pages refer to a couple handlers. Let's implement those, too. They're
pretty rote; read some data from the request, read some data from Stripe, and
redirect to the Stripe-hosted page:

`app/api/checkout/checkout-session/route.ts`

```ts
import { stripeClient } from "@/utils/stripe/stripe";
import { createClient } from "@/utils/supabase/server";
import { NextRequest } from "next/server";
import { z } from "zod";

const BodyValidator = z.object({
  id: z.string(),
  priceId: z.string(),
});

export async function POST(req: NextRequest) {
  const body = Object.fromEntries((await req.formData()).entries());
  const bodyResult = BodyValidator.safeParse(body);
  const user = await createClient().auth.getUser();

  // No user -> redirect to sign in
  if (user.error) {
    return Response.redirect(`${process.env.NEXT_PUBLIC_BASE_URL}/sign-in`);
  }

  // Invalid body -> 400
  if (!bodyResult.success) {
    return Response.json({ error: bodyResult.error }, { status: 400 });
  }

  const price = await stripeClient.prices.retrieve(bodyResult.data.priceId);

  const session = await stripeClient.checkout.sessions.create({
    line_items: [
      {
        price: price.id,
        quantity: 1,
      },
    ],
    customer_email: user.data.user.email,
    mode: price.recurring ? "subscription" : "payment",
    success_url: `${process.env.NEXT_PUBLIC_BASE_URL}/pricing/success`,
    cancel_url: `${process.env.NEXT_PUBLIC_BASE_URL}/pricing`,
  });

  if (!session || !session.url) {
    return Response.json(
      { error: "Failed to create session" },
      { status: 500 }
    );
  }

  return Response.redirect(session.url, 303);
}
```

`app/api/checkout/portal-session/route.ts`

```ts
import { stripeClient } from "@/utils/stripe/stripe";
import { createClient } from "@/utils/supabase/server";
import { NextRequest } from "next/server";

export async function POST(req: NextRequest) {
  const user = await createClient().auth.getUser();

  if (user.error) {
    return Response.redirect(`${process.env.NEXT_PUBLIC_BASE_URL}/sign-in`);
  }

  // Find the customer by email
  const customerResponse = await stripeClient.customers.search({
    query: `email:'${user.data.user.email}'`,
    limit: 1,
  });

  // Take the first one we find (we should only have one if things work right)
  const [customer] = customerResponse.data;

  if (!customer) {
    return Response.json(
      { error: "Failed to retrieve customer" },
      { status: 500 }
    );
  }

  // Get the portal URL from Stripe
  const billingSession = await stripeClient.billingPortal.sessions.create({
    customer: customer.id,
    return_url: `${process.env.NEXT_PUBLIC_BASE_URL}/pricing`,
  });

  if (!billingSession || !billingSession.url) {
    return Response.json(
      { error: "Failed to create session" },
      { status: 500 }
    );
  }

  // Send them there
  return Response.redirect(billingSession.url, 303);
}
```

### Setting up a Stripe Webhook

In order to get information from Stripe, instead of relying on a potentially
malicious client to give that to us, instead we'll give a place to Stripe to
send our events. The main way to do this is Webhooks. We could also query Stripe
intermittently, but that's not as ideal.

Webhooks are really cool. In situations where you're building an app, they
provide a more secure way to get notified when specific events happen. For
consumers they're also great because they're pretty standard, and they just
require setting up an endpoint that can receive the POST request that will be
sent.

Let's build exactly that–an endpoint that will receive the POST request that
Stripe sends us.

#### Some Pre-Work

Let's do the supabase client next since it's pretty rote:

`utils/supabase/service.ts`

```ts
"use server";
import { createClient } from "@supabase/supabase-js";

export const createServiceClient = () =>
  createClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!,
    {
      auth: {
        persistSession: false,
        autoRefreshToken: false,
        detectSessionInUrl: false,
      },
    }
  );
```

Next, a stripe client helper. I'm going to export this as a global instance
that'll fail to import if the key is not set:

`utils/stripe/stripe.ts`

```ts
import "server-only";
import { Stripe } from "stripe";

const { STRIPE_SECRET_KEY } = process.env;
if (!STRIPE_SECRET_KEY) {
  throw new Error("STRIPE_SECRET_KEY was not provided");
}

export const stripeClient = new Stripe(STRIPE_SECRET_KEY);
```

These two will be poisoned by `server-only` if I try to import them on the
clientside which is what I want. I don't want to accidentally load my service
client or stripe client in a way that's accessible to the user.

#### The Webhook

And now for the handler. I'm going to write a handler for this rather than a
page, since all it needs to do is deal with a request:

`app/api/webhooks/stripe/route.ts`

```ts
import { stripeClient } from "@/utils/stripe/stripe";
import { NextRequest } from "next/server";

export async function POST(request: NextRequest) {
  const body = await request.text();
  const signature = request.headers.get("stripe-signature");
  if (!body || !signature) {
    return Response.json({}, { status: 400 });
  }

  const secret = process.env.STRIPE_WEBHOOK_SECRET;
  if (!secret) {
    return Response.json({}, { status: 500 });
  }

  const event = stripeClient.webhooks.constructEvent(body, signature, secret);

  try {
    switch (event.type) {
      case "customer.subscription.created":
        handleSubscriptionCreated(event.data.object);
        break;
      default:
        console.log(`Unhandled event type ${event.type}.`);
    }
  } catch (ex) {
    console.error(ex);
    return Response.json({}, { status: 500 });
  }

  return Response.json({}, { status: 200 });
}

async function handleSubscriptionCreated(subscription: Stripe.Subscription) {
  console.log(`Subscription created: ${subscription.id}`);
  console.log(subscription);
  const [id, ...rest] = subscription.items.data.map((item) =>
    resolveString(item.plan.product)
  );
  console.log(id);
  if (!id) {
    throw new Error(`No product ID found: ${id}`);
  }

  const customerId = resolveString(subscription.customer);

  const customer = await stripeClient.customers.retrieve(customerId);
  if (customer.deleted) {
    console.log(`Customer ${customerId} deleted`);
    return;
  }

  if (!customer.email) {
    console.log(`Customer ${customerId} has no email`);
    return;
  }

  if (!(await updateLicense(customer.email, License.ByProductId[id].id))) {
    throw new Error(`Failed to update license for ${customer.email}`);
  }
}
```

Now, I like separating my code into reusable pieces. Normally, I'd use
dependency injection but Next.js doesn't like that, so I'll have to settle for
exported functions.

### Listening

You'll want to set up the [stripe CLI](https://docs.stripe.com/stripe-cli) if
you haven't already.

```
stripe listen --forward-to localhost:3000/api/webhooks/stripe
```

## Notes on Design
