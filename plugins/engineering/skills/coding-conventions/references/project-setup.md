# Project Setup

What a new repository gets, and why. For an existing project, read its `CLAUDE.md` and follow what is already there.

---

## The stack

| Concern | Choice |
|---|---|
| Language | TypeScript, `strict: true` |
| Linting | ESLint with `typescript-eslint` type-aware rules |
| Formatting | Prettier |
| Framework lint rules | `eslint-config-next` on Next.js projects |

Type-aware linting requires ESLint to know about your `tsconfig.json`. It is slower than syntactic linting and worth it — the rules that need type information are the ones that catch real bugs rather than style drift.

## Why ESLint and Prettier rather than Biome

Biome is a single fast tool covering both lint and format, and the speed is genuinely attractive when a linter runs on every turn of an agent session. It is not the choice here, for two reasons that have nothing to do with speed.

**Rule coverage.** Type-aware rules are exactly the ones that catch mistakes generated code makes — unawaited promises, unsafe assignments. Biome's own benchmark puts `noFloatingPromises` at roughly 75% of the cases `typescript-eslint` detects. Next.js rules are unimplemented, so there is no `eslint-config-next` equivalent. Biome's migration guide states plainly that exact parity is a non-goal: some rule options are deliberately not implemented, and some rules deviate from the original.

**File-type coverage.** Prettier formats Markdown, YAML, and Vue/Svelte/Astro today. Biome does not format Markdown — roadmapped since 2024, still unshipped — has no YAML support at all, and keeps Vue/Svelte/Astro behind an experimental flag with an HTML parser documented as unstable. On a repository that is mostly Markdown and YAML, that gap is most of the work.

Biome does ship `useExhaustiveDependencies`, recommended and erroring by default, so the single most valuable React hooks rule is covered.

> **Revisit this** when Biome ships stable Markdown formatting or closes the type-aware gap. `biome migrate eslint --write --include-inspired` makes the switch cheap, which is precisely why this is a note rather than a decision record.

## The lint hook matters more than the config

An agent does **not** reliably read `eslint.config.js` before generating code. It might read the file opportunistically, but a config assembled from `extends` is effectively invisible — the rules live inside packages it would have to resolve and read first.

So the lint config cannot be the only enforcement. Configure the linter to run automatically as a hook:

- Prefer a hook that fires **at the end of a turn** over one that fires on every file write. Per-write linting is noisy during a refactor, when intermediate states are expected to be broken.
- Feed the output back rather than only surfacing it. An error the agent cannot see is an error it cannot fix.

This is the only deterministic layer. Instructions shape the first draft; the linter decides what actually ships.

## Give the project's `CLAUDE.md` a "What we're not using" section

The single highest-value section in a project's `CLAUDE.md`. Models suggest popular libraries confidently, including ones the project has deliberately rejected, and correcting the same suggestion repeatedly is a tax on every session.

List the rejection and the replacement:

```markdown
## What we're not using

- ~~Axios~~ → native `fetch`, no extra dependency
- ~~Redux~~ → Zustand, simpler API for the amount of shared state here
- ~~Moment.js~~ → `date-fns`, tree-shakeable and maintained
- ~~Pages Router~~ → App Router only
```

Keep it in `CLAUDE.md` rather than a `docs/` file. It needs to be in context before the model writes anything, not fetched after it has already guessed.

## Keep the rest of `CLAUDE.md` lean

It loads on every call. Rules, commands, constraints, and pointers belong there; anything longer belongs in `docs/` and gets linked. If it grows past roughly a hundred lines, something in it wants moving.
