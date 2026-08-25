# decision-record

Writes a MADR-style Decision Record for a contested, costly-to-reverse decision that cannot be recovered from the code alone.

## When to use

Use this when you need to record *why* a significant decision was made — not what was decided (the code shows that), but the reasoning that a future reader or agent could not infer.

**Three scopes:**

| Scope | Covers |
|---|---|
| `architecture` | Technical decisions: data model, API design, infrastructure, framework choice, security model |
| `product` | Business decisions: pricing, scope cuts, target-user calls, build-vs-buy |
| `process` | How the team works: ceremonies, technical practices, team agreements |

**Do not use** for decisions that are cheap to reverse, obvious from the code, or where a code comment or PR description carries the rationale adequately. The skill will refuse via its significance gate.

## Inputs

| Input | Required | Notes |
|---|---|---|
| Decision description | Yes | What was decided |
| Context and background | Yes | What triggered the decision |
| Alternatives considered | Yes | Real options that were genuinely weighed |
| Relevant docs | No | PRD, tech spec, codebase context |

## Output

A MADR-format Decision Record written to:

```
docs/decisions/<scope>/NNNN-<slug>.md
```

Numbered sequentially within each scope folder. Immutable `status:` field (`proposed` → `accepted` → `superseded`).

## How it works

1. Reads the decision context
2. Applies the **significance gate** — argues itself out of writing first; refuses if any test fails
3. Determines scope (`architecture` / `product` / `process`)
4. Runs the appropriate **review-lens subagent(s)** to pressure-test the gate
5. Numbers the file sequentially within the scope folder
6. Fills the MADR template and writes the record

## The significance gate

A Decision Record is only warranted when **all three** hold:

1. **It was contested** — real alternatives existed with genuine trade-offs
2. **It is expensive to reverse** — touches data model, public API, vendor lock-in, security model, or money
3. **The "why" is not recoverable from the code** — a future reader would ask "why did they do it this way?"

Default answer is **no**. The skill argues itself out first.

## Usage guidelines

| Setting | Recommendation |
|---|---|
| Model | **claude-sonnet-4-6** for most decisions. **claude-opus-4-7** for complex architectural trade-offs with many interacting constraints. |
| Effort | **High.** The significance gate and review-lens subagents require careful reasoning. |

Rough ceiling: more than ~1 Decision Record per significant feature usually means you are recording non-decisions.

## Pipeline

This skill is standalone and can be triggered at any stage. Link from the relevant code with a comment:

```
// see docs/decisions/architecture/0001-postgres-over-dynamodb.md
```

## Files

| File | Purpose |
|---|---|
| `SKILL.md` | Skill instructions |
| `README.md` | This file |
| `decision-record-template.md` | MADR output template |
| `prompts/technical-lens.md` | Technical review-lens persona (architecture/process scope) |
| `prompts/product-lens.md` | Product review-lens persona (product scope) |
