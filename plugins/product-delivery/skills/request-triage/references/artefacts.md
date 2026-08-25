# Artefacts: where they go and how long they live

Two kinds of document come out of this plugin, and confusing them is what makes a documentation set go stale.

**Durable** documents describe how things *are*. They are written in the present tense, they live beside the code, and they are true until the code changes. The glossary, the domain model, decision records, the non-functional baseline.

**Transient** documents describe a change we *propose to make*. They are written in the future tense, they are true only until the change ships, and after that they are a description of a plan that no longer exists. Requirements documents, technical specifications, delivery plans.

Keeping them in the same folder is how a repository ends up with a `docs/` directory nobody trusts. An agent reading a shipped feature's requirements document has no way to know it is describing the past.

---

## Layout

```
docs/
├── product/                          DURABLE
│   ├── vision.md                     product-definition
│   ├── product-backlog.md            product-definition; appended by request-triage
│   ├── glossary.md                   domain-modelling; appended by acceptance-review
│   ├── domain-model.md               domain-modelling; appended by acceptance-review
│   └── nfr.md                        product-definition; amended by acceptance-review
├── decisions/<scope>/NNNN-<slug>.md  DURABLE — decision-record; fed by acceptance-review
└── work/                             TRANSIENT
    ├── 2026-08-csv-export/           one folder per work order
    │   ├── work-order.md             request-triage
    │   ├── requirements.md           backlog-refinement
    │   ├── tech-spec.md              technical-design
    │   ├── delivery-plan.md          delivery-planning
    │   └── tickets/                  work-breakdown
    └── _archive/
        ├── README.md
        └── 2026-08-stripe-billing/   moved here by acceptance-review
```

The work folder is named `YYYY-MM-<slug>` — the year and month it was opened, then a short kebab-case slug. The date prefix is not decoration: it is what makes a cold folder obvious at a glance and gives you something to prune by.

---

## These are defaults, not fixed paths

**Before writing anything, check the host project's `AGENTS.md` or `CLAUDE.md` for its own conventions.** If the project puts documentation somewhere else, follow the project. The layout above is what to use when the project says nothing.

This matters more than it looks. A skill that hardcodes a folder tree only works in repositories that already agreed to it.

---

## Reading legacy layouts

Earlier versions of this plugin wrote to `docs/features/<slug>/` with `prd.md` rather than `requirements.md`. Work started under that layout is still valid.

When looking for an input document:

1. Look in `docs/work/YYYY-MM-<slug>/` first.
2. Fall back to `docs/features/<slug>/`, and accept `prd.md` where you expected `requirements.md`.
3. If you find one, say which layout you are reading and carry on. **Do not migrate it** — moving files out from under work that is in flight breaks the links in tickets that already exist.

New work orders always use the current layout.

---

## The end of a transient document

`acceptance-review` closes a work order out. It moves the folder to `docs/work/_archive/<slug>/` and stamps every file:

```yaml
status: superseded
archived: YYYY-MM-DD
harvested_to: [docs/product/glossary.md, docs/decisions/architecture/0007-webhook-retries.md]
```

It never deletes. Archived work is deleted by hand, once you are satisfied nothing was lost.

Nothing should link *into* `_archive/`. `AGENTS.md` entries point at live work folders and are removed when the work is accepted; tickets link to durable documents, not transient ones. An archived document that something still depends on was not finished being harvested.
