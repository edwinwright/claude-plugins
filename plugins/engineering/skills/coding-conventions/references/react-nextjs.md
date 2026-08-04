# React and Next.js

Structural preferences for App Router projects. Check the host project's `CLAUDE.md` first — an existing project's layout always wins.

---

## Project structure

Use a `src/` directory. It keeps application code separate from the growing pile of config files at the repository root, and makes "everything under `src/` is ours" a rule with no exceptions to remember.

```
src/
  app/          routes, layouts, route handlers — routing only
  features/     one directory per feature: components, hooks, logic, types
  components/   shared UI used by two or more features
  lib/          framework-agnostic utilities and clients
  services/     data access — the only place that talks to a database or external API
```

### Group by feature, not by file type

A `components/` directory holding every component in the application tells you nothing about what the application does, and every change touches four sibling directories at once.

Group by feature instead. Everything a feature needs lives in its directory until a second feature needs it, at which point it moves up to `components/` or `lib/`. **Promote on the second use, not in anticipation of it.**

```
src/features/checkout/
  components/
  hooks/
  checkout-service.ts
  types.ts
```

The tell that a feature directory has gone wrong: you cannot delete it without breaking unrelated features.

### Keep routing thin

Files under `app/` handle routing, layout, and data loading, then delegate. Business logic in a page component cannot be tested without rendering a route, and cannot be reused by a second route.

---

## Server and client components

**Server Component is the default.** Reach for `'use client'` only when the component needs interactivity, browser APIs, or React state and effects.

- **Push `'use client'` down the tree.** Marking a page as a client component drags everything it renders across with it. Mark the small interactive leaf instead — the button, not the page containing it.
- **Fetch in Server Components** with `async`/`await` directly. A client-side fetch for data that could have been rendered on the server buys a loading spinner nobody wanted.
- **Server Actions for mutations**, in preference to a route handler that exists only to be called by one form.
- **Never let a client component import from `services/`.** That is how a database client ends up in a browser bundle. Data crosses the boundary as props or through a Server Action.

## Data access

All database and external API access goes through `services/`. Components — server or client — never construct a query.

This is worth the indirection because it gives one place to add caching, one place to change when the provider changes, and one place to look when a query is slow. It also means a component's test does not need a database.

## State

- **The URL is state.** Filters, tabs, pagination, and search terms belong in search params, where they survive a refresh and can be shared as a link.
- **Server state is not client state.** Data owned by the server wants a cache with revalidation, not a `useState` and an effect that fetches on mount.
- **Reach for context late.** Prop drilling through two levels is clearer than a context; through five it is not. Passing components as props often removes the problem entirely.
- **`useEffect` is for synchronising with something outside React.** Deriving a value from props or state during render is not a synchronisation problem, and an effect that only computes is a re-render you did not need.
