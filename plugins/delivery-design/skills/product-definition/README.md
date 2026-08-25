# product-definition

Turns a product idea into a Product Brief / Vision document and a MoSCoW-prioritised `product-backlog.md`.

## When to use

Use this at the start of a new product, before any feature work begins — or cold, to revise an existing product definition.

Invoke it with phrases like:
- "I have an idea for a new product..."
- "Write a product brief for..."
- "I want to build..."
- "What should I build?"

For a new feature on an **existing product** (the product definition already exists), start at `request-triage` instead.

## Inputs

| Input | Required | Notes |
|---|---|---|
| Product idea description | Yes | Can be brief or detailed |
| Target user description | No | Will be asked if missing |
| Competitors or prior art | No | Helps frame differentiation |
| Constraints | No | Technical, regulatory, commercial |

## Output

Two files written to `docs/product/`:

```
docs/product/
  vision.md              ← Product Brief (vision, target users, problem, success metrics, principles, constraints)
  product-backlog.md     ← MoSCoW-prioritised feature list
```

Each backlog entry has a title, one-sentence user value, and an optional scope note — sufficient for `backlog-refinement` to open without further context.

## How it works

1. Reads the input and identifies what is clear and what is missing
2. Checks for any existing product context
3. Asks targeted clarifying questions (max 7)
4. Writes the Product Brief using `vision-template.md`
5. Writes the product backlog using `product-backlog-template.md`
6. Advises on next steps (domain-modelling → backlog-refinement)

## Usage guidelines

| Setting | Recommendation |
|---|---|
| Model | **claude-sonnet-4-6** is sufficient. Use **claude-opus-4-7** for complex multi-sided products or where the business model needs careful analysis. |
| Effort | **Low to medium.** This is a structured writing task. |

The quality of the output depends on what you provide upfront. The more context about users, constraints, and goals, the fewer questions the skill needs to ask.

## Routes

Runs at the head of the **Full** route, once per product rather than once per piece of work. Also standalone — run it cold to revise an existing product definition.

Routes, escalation gates, and the work order format are defined in [`request-triage/references/routes.md`](../request-triage/references/routes.md).

**Next step:** Run `domain-modelling` to bootstrap the glossary and domain model. Then run `request-triage` on each backlog item.

## Files

| File | Purpose |
|---|---|
| `SKILL.md` | Skill instructions |
| `README.md` | This file |
| `vision-template.md` | Output template for `docs/product/vision.md` |
| `product-backlog-template.md` | Output template for `docs/product/product-backlog.md` |
