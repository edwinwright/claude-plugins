# Archived work orders

> [!warning] Superseded — not current
> Everything in this directory describes work that has already shipped. These are **plans**, written before the code existed. They do not describe the system as it is now.
>
> **Do not read these to understand how the system works.** For that, use:
>
> - `docs/product/glossary.md` — what terms mean here
> - `docs/product/domain-model.md` — entities, relationships, invariants
> - `docs/product/nfr.md` — the non-functional baseline
> - `docs/decisions/` — why things are the way they are
> - the code

Each folder was moved here by `acceptance-review` once its work was verified. Every file carries frontmatter recording that:

```yaml
status: superseded
archived: YYYY-MM-DD
harvested_to: [the durable documents its content moved into]
```

`harvested_to` is the useful field. Anything worth keeping was promoted into the documents listed there — that is what archiving means here, and it is why these folders are safe to delete.

## Deleting

They are kept only so you can check nothing was lost. Once you are satisfied:

```
rm -rf docs/work/_archive/<folder>
```

Git holds the history either way.

## Nothing should link here

`AGENTS.md` points at live work orders and its entries are removed at acceptance. Tickets link to durable documents. If something still depends on a folder in here, it was not finished being harvested — reopen it rather than restoring the link.
