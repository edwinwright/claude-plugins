# React

Rules for any React codebase, web or native. Read the runtime file alongside this one: `nextjs.md` for Next.js App Router, `react-native.md` for React Native and Expo. Check the host project's `CLAUDE.md` first — an existing project's layout always wins.

---

## Project structure

Use a `src/` directory. It keeps application code separate from the growing pile of config files at the repository root, and makes "everything under `src/` is ours" a rule with no exceptions to remember.

```
src/
  app/          routes and layouts only — file-based routing in Next.js and Expo Router alike
  modules/      one directory per feature: components, hooks, logic, types
  components/   shared UI used by two or more features
  lib/          framework-agnostic utilities and clients
  services/     data access — the only place that talks to a database or external API
```

### Group by feature, not by file type

A `components/` directory holding every component in the application tells you nothing about what the application does, and every change touches four sibling directories at once.

Group by feature instead. Everything a feature needs lives in its directory until a second feature needs it, at which point it moves up to `components/` or `lib/`. **Promote on the second use, not in anticipation of it.**

```
src/modules/checkout/
  components/
  hooks/
  checkout-service.ts
  types.ts
```

The tell that a feature directory has gone wrong: you cannot delete it without breaking unrelated features.

### Keep routing thin

Files under `app/` handle routing and layout, then delegate. Business logic in a route component cannot be tested without rendering a route, and cannot be reused by a second route.

---

## Data access

All database and external API access goes through `services/`. Components never construct a query or a request.

This is worth the indirection because it gives one place to add caching, one place to change when the provider changes, and one place to look when a query is slow. It also means a component's test does not need a database or a network.

## State

- **The URL is state.** Filters, tabs, pagination, and search terms belong in search params or route params, where they survive a refresh and can be shared as a link.
- **Server state is not client state.** Data owned by the server wants a cache with revalidation, not a `useState` and an effect that fetches on mount.
- **Reach for context late.** Prop drilling through two levels is clearer than a context; through five it is not. Passing components as props often removes the problem entirely.
- **`useEffect` is for synchronising with something outside React.** Deriving a value from props or state during render is not a synchronisation problem, and an effect that only computes is a re-render you did not need.

## Memoisation

**Let the React Compiler memoise.** Where the compiler is enabled, do not write `useMemo`, `useCallback`, or `React.memo` by default. Reach for them only as an escape hatch, when a measured problem or a reference-identity requirement (an effect dependency, a third-party API that compares by reference) needs precise control. Where the compiler is not enabled, memoise only what profiling shows is expensive.

Hand-written memoisation in compiled code is noise the next reader has to reason about, and it is usually less precise than what the compiler produces.
