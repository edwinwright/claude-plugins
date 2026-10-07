# [Product Name] — Agent Instructions

## What this is

One sentence describing what this product is. → docs/product/vision.md

## Before you start — read for your task

- Working on a work order → its `docs/work/<ID>-<slug>/PLAN.md`. Do one slice at a time, as its How to run section says.
- Touching a domain entity or term → `docs/domain/domain-model.md` + `docs/domain/glossary.md`
- Meeting a performance, accessibility, or security bar → `docs/product/nfr.md`
- Choosing a library, pattern, or structure → `docs/architecture/tech-stack.md` + `docs/architecture/conventions.md`
- Making an architectural change → `docs/architecture/decisions/`
- Adding a new feature → `docs/product/product-backlog.md` + `docs/product/vision.md`

Entries under `docs/work/` are transient and are removed when the work order is accepted. Nothing here should ever point into `docs/work/_archive/`.

## Work orders

Work order prefix: [PREFIX, e.g. MD. Omit this line if IDs come from a connected tracker.]

## Commands

```
build:  [command]
test:   [command]
run:    [command]
lint:   [command]
```

## Hard rules

Only the non-inferable few. If it would be obvious from a clean reading of the code, it does not belong here.

- 

## Skills

Situational procedures live in skills — not above. Only listed here so agents can invoke them when triggered.

- [skill name] — [one-line description of when to use it]
