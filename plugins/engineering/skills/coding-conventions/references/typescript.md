# TypeScript

Judgement rules only. Mechanical rules — `no-explicit-any`, `prefer-const`, `no-var`, import ordering, naming casing — belong to the lint config and are not repeated here.

---

## Naming

A name's job is to make the next line predictable.

- **Booleans read as a question.** `isLoading`, `hasError`, `canEdit`. If the name does not answer yes or no, it is not a boolean — it is a state, and it wants a union.
- **Functions are verbs; values are nouns.** `getUser` returns, `renderUser` draws, `user` holds.
- **Say what it is, not what it is made of.** `deadline` beats `dateValue`. Encoding the type in the name duplicates what the type already says and rots when the type changes.
- **Match the domain's word.** If the business calls it a *booking*, the code says `booking` — never a synonym invented for the code. Consistency with the domain is worth more than an objectively better word.

## Functions

- **One thing.** The test is whether you can name it without "and". `validateAndSave` is two functions.
- **Extract when it stops fitting.** Around thirty lines is the point where a function usually deserves splitting, but the real signal is needing a comment to explain a *section* — that section is the function you should have extracted.
- **Prefer early returns.** Guard clauses at the top, happy path unindented at the bottom. Deep nesting is almost always an unextracted function or an unhandled case pretending to be structure.
- **Isolate side effects.** Push I/O to the edges and keep the logic between them pure. A function that both computes and persists is hard to test and harder to reuse.

## Types

- **Model the domain, do not describe the shape.** Types are the cheapest documentation available; spend them on meaning.
- **Prefer a union over a boolean plus optionals.** `{ status: 'loading' } | { status: 'error'; message: string } | { status: 'ready'; data: User }` makes invalid states unrepresentable. `{ isLoading: boolean; error?: string; data?: User }` permits eight combinations, of which five are nonsense.
- **`unknown` at the boundary, a real type immediately after.** Anything crossing a network or file boundary arrives untrusted — parse it into a known type at the edge, and let everything inside rely on that type.
- **Do not reach for a generic until the second caller.** A generic written for one call site is a guess about what the second one will need, and it is usually wrong.

```typescript
type RequestState =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'error'; message: string }
  | { status: 'ready'; data: User };
```

## Comments

- **Explain why, never what.** The code says what. A comment restating it becomes a lie at the next edit.
- **Comment the surprising.** A workaround, a non-obvious ordering constraint, a deliberate deviation from the obvious approach — those earn a comment. Nothing else does.
- **`TODO` carries a reason and a condition.** `// TODO: replace once the auth middleware lands` is actionable. A bare `// TODO: fix` is noise that will outlive the project.

## Errors

- **Never swallow.** An empty `catch` discards the only evidence of what went wrong.
- **Catch only what you can handle.** If the handler cannot do anything useful, let it propagate to somewhere that can. Catching to log and rethrow unchanged usually just adds noise.
- **Add context when rethrowing.** The stack says where; a wrapped error says what was being attempted and with what.
- **Fail fast on programmer error, degrade on user error.** A malformed API response should throw loudly. A user typing a bad postcode should not.
