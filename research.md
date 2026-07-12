# Research

## Codex Conversation: Suggest SvelteKit stack

> i plan to do a website using svelte and sveltekit. the database should be a postgresql, the
> hosting will be done via vercel and neon.
>
> the website should be a rather simple recipe browser with almost no social elements, a way to
> create a cooking plan for a week with a summarized groceries shopping list as well as some recipe
> management possibilities.
>
> i have not decided on solutions for the following problems:
>
> - i18n
> - component library
> - authentication
>
> what are the most common solutions for each of the three things in sveltekit applications and what
> should i think about that i might forget?
>
> give me some suggestioins

<details><summary>2 previous messages</summary>

> I’ll verify the current SvelteKit ecosystem names and project status first, since auth and
> component libraries in particular have shifted over the last couple of years.
>
> <details><summary>Searched the web</summary>
>
> <details><summary>Searched the web</summary>
>
> - Searched the web for
>   `SvelteKit internationalization Paraglide svelte-i18n typesafe-i18n current 2026`
> - Searched the web for `SvelteKit i18n Paraglide JS SvelteKit official`
> - Searched the web for `https://authjs.dev/reference/sveltekit`
> - Searched the web for `https://lucia-auth.com/`
> - Searched the web for `https://www.shadcn-svelte.com/`
> - Searched the web for `https://flowbite-svelte.com/`
> - Searched the web for `https://github.com/ivanhofer/typesafe-i18n`
> - Searched the web for `svelte-i18n GitHub README Svelte internationalization library`
> - Searched the web for `site:svelte.dev/docs/kit i18n SvelteKit`
>
> </details>
>
> </details>

</details>

For this stack, I would lean toward boring, SvelteKit-native choices.

**Short recommendation**

| Area              | My default pick            | Why                                                                              |
| ----------------- | -------------------------- | -------------------------------------------------------------------------------- |
| i18n              | `Paraglide JS`             | Current SvelteKit-friendly option, typed messages, SSR/localized routing support |
| Component library | `shadcn-svelte` + Tailwind | Good balance: polished defaults, editable code, based on accessible primitives   |
| Auth              | `Better Auth`              | Svelte docs point to it, works well when you own the DB on Neon/Postgres         |

Sources checked: SvelteKit auth docs, Better Auth SvelteKit docs, Auth.js SvelteKit docs, Paraglide
SvelteKit docs, shadcn-svelte, Bits UI, Skeleton, Flowbite Svelte.

**i18n**

Common choices:

- `Paraglide JS`: probably the strongest current default. Its docs describe it as SvelteKit’s
  official i18n integration, compiler-based, type-safe, with localized URLs and SSR/SSG support.
  Good if recipes should be indexable by Google in multiple languages. Source:
  https://paraglidejs.com/sveltekit

- `svelte-i18n`: older, simple runtime-store based approach. Fine for small apps, less attractive if
  you care about localized routing, type safety, SEO, and keeping bundles lean.

- `typesafe-i18n`: type-safe and lightweight, supports SvelteKit, but I would now consider Paraglide
  first for new SvelteKit projects unless you strongly prefer its generated API. Source:
  https://github.com/codingcommons/typesafe-i18n

Things easy to forget:

- Recipes are not just UI strings. Ingredient names, units, prep steps, cuisine tags, categories,
  and slugs may need localization.
- Decide early whether URLs are localized: `/de/rezepte/...` vs `/recipes/...` with a cookie. For
  public recipe browsing, localized URLs are usually better.
- Units are localization too: metric/imperial, grams/cups, Celsius/Fahrenheit.
- Search needs a plan per language. Postgres full-text search is language-aware, but multilingual
  recipe search can get messy.
- Add correct `<html lang>` and possibly `dir`; Paraglide has setup for this.
- Recipe slugs should probably be stored separately from titles so title edits do not break old
  links.

**Component Library**

Common choices:

- `shadcn-svelte`: polished components, copied into your repo, easy to customize, good default for a
  small product-like app. It is “open code”, not a black-box package. Source:
  https://www.shadcn-svelte.com/

- `Bits UI`: headless, accessible primitives. Great if you want to design your own UI, more work
  than shadcn-svelte. Source: https://www.bits-ui.com/

- `Skeleton`: fuller Svelte/Tailwind design system with many components and themes. Faster if you
  like its visual system. Source: https://www.skeleton.dev/

- `Flowbite Svelte`: ready-made Tailwind components, lots included, good for speed, but the visual
  style is more recognizably “Flowbite”. Source: https://flowbite-svelte.com/

My pick for your app: `shadcn-svelte` plus Tailwind. A recipe browser needs normal app UI: filters,
dialogs, forms, tabs, dropdowns, cards, shopping-list checkboxes, maybe a planner grid.
shadcn-svelte gives you good building blocks without forcing a heavy visual identity.

Things easy to forget:

- Mobile matters a lot for recipes and shopping lists. Check the planner and grocery list on phone
  widths early.
- You may want print styles for recipes and weekly plans.
- Shopping lists need good checkbox/touch behavior, not just a nice desktop layout.
- Recipe images need fixed aspect ratios and fallbacks.
- shadcn-style components are copied into your codebase, so upgrades are manual. That is often a
  feature, but you own the code.
- Consider `sveltekit-superforms` + `zod` for recipe creation/editing forms. Component libraries do
  not solve validation, progressive enhancement, or server actions.

**Authentication**

Common choices:

- `Better Auth`: current strong default. SvelteKit docs say the Svelte CLI can set up Better Auth,
  and Better Auth has a SvelteKit integration. Good if you want auth tables in your Neon Postgres
  DB. Sources: https://svelte.dev/docs/kit/auth and
  https://better-auth.com/docs/integrations/svelte-kit

- `Auth.js` / `@auth/sveltekit`: familiar if you know NextAuth/Auth.js. It supports many providers
  and has adapters, including Neon/Postgres-related ones, but its own SvelteKit docs mark
  `@auth/sveltekit` as experimental and say Auth.js is now part of Better Auth. Source:
  https://authjs.dev/reference/sveltekit

- Hosted auth: Clerk, Auth0, Supabase Auth, Kinde, etc. Useful if you want less
  security/account-management burden, but adds vendor coupling and often feels like overkill for a
  simple recipe app.

- Lucia-style custom sessions: Lucia is now more of an auth learning/reference resource than a
  drop-in framework. Good if you want to understand and own everything, but I would not start there
  unless auth is part of what you want to learn. Source: https://lucia-auth.com/

My pick for your app: `Better Auth` with database-backed sessions in Neon. Start with email/password
or magic link. Add OAuth later only if you actually want it.

Things easy to forget:

- Decide if anonymous users can build a temporary weekly plan before signing up. This affects schema
  and UX.
- Use database sessions if you want immediate logout/revocation. SvelteKit docs note DB sessions are
  revocable but cost a DB lookup; JWTs are lighter but not immediately revocable.
- Protect recipe ownership carefully: private recipes, user recipes, shared/public recipes.
- Add rate limiting for login/signup/password reset.
- Email delivery is part of auth if you use magic links or password resets. You will need Resend,
  Postmark, Mailgun, etc.
- On Vercel, make sure callback URLs, trusted hosts, cookies, secrets, and preview deployments are
  configured correctly.
- Do not put auth-dependent data in client-only checks. Enforce ownership in server load functions,
  actions, and API endpoints.

My suggested stack:

```txt
SvelteKit
Postgres on Neon
Drizzle ORM
Better Auth
Paraglide JS
Tailwind + shadcn-svelte
zod + sveltekit-superforms
```

For your use case, the hardest domain problem will probably not be the libraries. It will be the
recipe data model: ingredients, quantities, servings scaling, unit conversion, weekly plan slots,
and grocery-list aggregation. Decide that model early before polishing the UI.

> why would i want to use drizzle orm or zod?

`Drizzle ORM` and `Zod` solve different problems.

**Drizzle ORM**

Use Drizzle if you want type-safe database access without hiding SQL too much.

For your recipe app, it helps with:

- Defining your Postgres schema in TypeScript.
- Getting typed query results, inserts, joins, and relations.
- Generating migrations instead of manually tracking schema changes.
- Avoiding lots of raw SQL string mistakes.
- Keeping your app close to Postgres concepts, unlike heavier ORMs.

Example benefit: if `recipes.title` is required, or `recipe_ingredients.quantity` is numeric,
TypeScript can catch bad inserts before runtime.

You might skip Drizzle if:

- You prefer writing raw SQL directly.
- Your schema is tiny and you are comfortable managing migrations yourself.
- You want a richer high-level ORM with more abstraction, in which case Prisma is the common
  alternative.

For SvelteKit + Neon/Postgres, Drizzle is a good fit because it is lightweight and works well in
serverless-style deployments.

**Zod**

Use Zod if you want runtime validation with TypeScript-friendly schemas.

TypeScript only checks your code while developing. It does not validate real user input. Zod
validates data at runtime.

For your recipe app, Zod helps with:

- Validating recipe creation/edit forms.
- Checking ingredient quantities, units, prep times, servings, tags, etc.
- Reusing the same schema for form validation and server-side validation.
- Producing structured validation errors for the UI.
- Validating external/imported recipe data if you ever add import features.

Example:

```ts
const recipeSchema = z.object({
	title: z.string().min(1),
	servings: z.number().int().positive(),
	prepMinutes: z.number().int().nonnegative(),
	ingredients: z.array(
		z.object({
			name: z.string().min(1),
			quantity: z.number().positive(),
			unit: z.enum(['g', 'kg', 'ml', 'l', 'piece', 'tbsp', 'tsp']),
		}),
	),
})
```

You might skip Zod if:

- You only have very simple forms.
- You use another validation library like Valibot, ArkType, or Yup.
- You are willing to hand-write validation logic.

My pragmatic take: use both, but for different boundaries.

- `Drizzle`: protects the database layer.
- `Zod`: protects user input and external data before it reaches your database.

For your app specifically, Zod is especially useful because recipe data looks simple but gets messy
quickly: quantities, units, optional notes, scaling, grocery aggregation, and localized fields all
benefit from explicit validation.

> if zod is taking care of the validation, what do i need superforms for?

Zod validates data. `sveltekit-superforms` handles the form workflow around that validation.

You do not strictly need Superforms. You can use Zod directly in SvelteKit form actions. Superforms
becomes useful when you want less repetitive form plumbing.

What Superforms adds:

- Connects Zod schemas to SvelteKit form actions.
- Preserves submitted values after validation errors.
- Gives structured field errors back to the page.
- Handles nested data better, like `ingredients[0].quantity`.
- Helps with progressive enhancement via `use:enhance`.
- Tracks form state: submitting, dirty, tainted, valid, posted.
- Makes client-side validation and server-side validation use the same schema.
- Reduces boilerplate for forms with many fields.

Without Superforms, you write more of this yourself:

```ts
const data = Object.fromEntries(await request.formData())
const result = recipeSchema.safeParse(data)

if (!result.success) {
	return fail(400, {
		values: data,
		errors: result.error.flatten(),
	})
}
```

That gets annoying once the form has arrays, nested ingredients, optional fields, file uploads, or
dynamic rows.

For your recipe app:

- Simple login form: plain SvelteKit + Zod is fine.
- Recipe editor with ingredients, instructions, tags, servings, prep time, images: Superforms is
  worth it.
- Weekly planner with repeated day/meal slots: Superforms can help.
- Grocery list checkboxes: probably no need.

So the split is:

```txt
Zod = what is valid?
Superforms = how does the form behave in SvelteKit?
```

I would use Zod either way. Add Superforms once your first complex form appears, especially the
recipe editor.
