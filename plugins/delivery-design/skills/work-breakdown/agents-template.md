# [Product Name] — Agent Instructions

## What this is

One sentence describing what this product is. → docs/product/vision.md

## Before you start — read for your task

- Implementing a story → that work order's `docs/work/<YYYY-MM-slug>/requirements.md` + `tech-spec.md`
- Touching a domain entity or term → `docs/product/domain-model.md` + `docs/product/glossary.md`
- Meeting a performance, accessibility, or security bar → `docs/product/nfr.md`
- Making an architectural change → `docs/decisions/architecture/`
- Adding a new feature → `docs/product/product-backlog.md` + `docs/product/vision.md`

Entries under `docs/work/` are transient and are removed when the work order is accepted. Nothing here should ever point into `docs/work/_archive/`.

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
