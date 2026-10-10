# Next.js

Rules specific to the Next.js App Router. Read `react.md` first for structure, data access, and state; this file covers only what the server/client split changes.

---

## Routing files load data

Files under `app/` may also load data before delegating. Fetch in the route's Server Component and pass the result down; keep the logic that shapes it in `modules/` or `services/`.

## Server and client components

**Server Component is the default.** Reach for `'use client'` only when the component needs interactivity, browser APIs, or React state and effects.

- **Push `'use client'` down the tree.** Marking a page as a client component drags everything it renders across with it. Mark the small interactive leaf instead — the button, not the page containing it.
- **Fetch in Server Components** with `async`/`await` directly. A client-side fetch for data that could have been rendered on the server buys a loading spinner nobody wanted.
- **Server Actions for mutations**, in preference to a route handler that exists only to be called by one form.
- **Never let a client component import from `services/`.** That is how a database client ends up in a browser bundle. Data crosses the boundary as props or through a Server Action.
