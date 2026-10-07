# Artefacts: where they go and how long they live

Two kinds of document come out of this plugin, and confusing them is what makes a documentation set go stale.

**Durable** documents describe how things *are*. They are written in the present tense, they live beside the code, and they are true until the code changes. The glossary, the domain model, decision records, the non-functional baseline.

**Transient** documents describe a change we *propose to make*. They are written in the future tense, they are true only until the change ships, and after that they are a description of a plan that no longer exists. Requirements documents, technical specifications, delivery plans.

Keeping them in the same folder is how a repository ends up with a `docs/` directory nobody trusts. An agent reading a shipped feature's requirements document has no way to know it is describing the past.

---

## Layout

```
AGENTS.md                             agent router (CLAUDE.md symlinks to it)
docs/
├── architecture/                     DURABLE: the technical lens
│   ├── tech-stack.md                 hand-written; read by technical-design
│   ├── conventions.md                hand-written; read by technical-design
│   └── decisions/NNNN-<slug>.md      decision-record (architecture and process scopes)
├── domain/                           DURABLE: the shared ground
│   ├── glossary.md                   domain-modelling; appended by acceptance-review
│   ├── domain-model.md               domain-modelling; appended by acceptance-review
│   └── context-map.md                optional, hand-written: the bounded contexts
├── product/                          DURABLE: the value lens
│   ├── vision.md                     product-definition
│   ├── product-backlog.md            product-definition; appended by request-triage
│   ├── nfr.md                        product-definition; amended by acceptance-review
│   └── decisions/NNNN-<slug>.md      decision-record (product scope)
└── work/                             TRANSIENT, and committed
    ├── <ID>-<slug>/                  one folder per work order
    │   ├── work-order.md             request-triage
    │   ├── requirements.md           backlog-refinement (Standard, Full)
    │   ├── tech-spec.md              technical-design (Full)
    │   ├── delivery-plan.md          delivery-planning (Full)
    │   └── PLAN.md                   work-breakdown (every route)
    └── _archive/
        ├── README.md
        └── <ID>-<slug>/              moved here by acceptance-review
```

Work folders are **committed**. A cloud or background agent only sees what is in the repository, so a work order kept outside it cannot be picked up by one.

---

## Work order IDs

A work folder is named `<ID>-<slug>`: the work order's ID, then a short kebab-case slug of three words at most (`MD-123-csv-export`). The ID follows the project's ticketing prefix, so the folder, the branch, the commits, and any tracker issue all carry the same identifier.

Find the ID in this order:

1. **The request arrived as a tracker issue.** Use that issue's ID.
2. **A tracker is connected but there is no issue yet.** Ask once whether to create one. Creating an issue is visible to other people, so do not do it without a yes. If yes, use the new issue's ID; if no, go on to 3.
3. **The host's `AGENTS.md` declares a work order prefix** (`Work order prefix: MD`). Use the prefix and the next free number: read the highest number in use across `docs/work/` *and* `docs/work/_archive/`, and add one. Read the highest rather than counting folders, which mis-numbers as soon as one is deleted.
4. **Neither.** Ask once for the prefix, use it, and tell the user to record it in `AGENTS.md` as `Work order prefix: <PREFIX>` so the question is not asked again.

A local prefix must differ from any tracker's own key. If the project's Linear team key is `MD` and work orders are numbered locally as `MD-5`, then `MD-5` names two different things. When a project has a tracker, take IDs from the tracker (steps 1 and 2); the local prefix is for projects without one.

The work order's `opened:` date stays in its frontmatter, so nothing is lost by not putting the date in the folder name.

---

## These are defaults, not fixed paths

**Before writing anything, check the host project's `AGENTS.md` or `CLAUDE.md` for its own conventions.** If the project puts documentation somewhere else, follow the project. The layout above is what to use when the project says nothing.

This matters more than it looks. A skill that hardcodes a folder tree only works in repositories that already agreed to it.

---

## Reading legacy layouts

Work and documents written under earlier versions of this plugin are still valid. When looking for an input:

| Look here first | Then fall back to |
|---|---|
| `docs/work/<ID>-<slug>/` | `docs/work/YYYY-MM-<slug>/` (version 3), then `docs/features/<slug>/` (version 1, which used `prd.md` for `requirements.md`) |
| `docs/domain/glossary.md`, `docs/domain/domain-model.md` | `docs/product/glossary.md`, `docs/product/domain-model.md` |
| `docs/architecture/decisions/`, `docs/product/decisions/` | `docs/decisions/<scope>/` |

If you find a document in a legacy location, say which one you are reading and carry on. **Do not migrate it unprompted.** Moving files out from under work that is in flight breaks the links in plans and tracker issues that already exist. Offer the user a list of `git mv` commands for a one-off migration instead, and let them run it.

New documents always use the current layout.

---

## The end of a transient document

`acceptance-review` closes a work order out. It moves the folder to `docs/work/_archive/<ID>-<slug>/` and stamps every file:

```yaml
status: superseded
archived: YYYY-MM-DD
harvested_to: [docs/domain/glossary.md, docs/architecture/decisions/0007-webhook-retries.md]
```

It never deletes. Archived work is deleted by hand, once you are satisfied nothing was lost.

Nothing should link *into* `_archive/`. `AGENTS.md` entries point at live work folders and are removed when the work is accepted; plans and tracker issues link to durable documents, or to a live `PLAN.md`, never into the archive. An archived document that something still depends on was not finished being harvested.
