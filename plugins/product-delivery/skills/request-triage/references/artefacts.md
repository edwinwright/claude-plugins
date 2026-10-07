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
    ├── WO-NNNN-<slug>/               one folder per work order
    │   ├── work-order.md             request-triage
    │   ├── requirements.md           backlog-refinement (Standard, Full)
    │   ├── tech-spec.md              technical-design (Full)
    │   ├── delivery-plan.md          delivery-planning (Full)
    │   └── PLAN.md                   work-breakdown (every route)
    └── _archive/
        ├── README.md
        └── WO-NNNN-<slug>/           moved here by acceptance-review
```

Work folders are **committed**. A cloud or background agent only sees what is in the repository, so a work order kept outside it cannot be picked up by one.

---

## Work order IDs

Every work order is numbered `WO-NNNN`, zero-padded to four digits: `WO-0001`, `WO-0002`. Its folder is `docs/work/WO-NNNN-<slug>/`, where the slug is short and kebab-case, three words at most (`WO-0004-csv-export`).

To number a new work order, read the highest `WO-` number in use across `docs/work/` **and** `docs/work/_archive/`, and add one. Read the highest rather than counting folders, which mis-numbers as soon as one is deleted. If there is no `WO-` folder yet, start at `WO-0001`. Folders from earlier layouts, which have no `WO-` prefix, do not count.

The only clash possible is with a tracker whose own team key is `WO`. Give tracker teams a three-letter key (`MIT`, not `WO`).

When a request arrives as a tracker issue, record it in the work order's frontmatter as `source: MIT-42`.

### Branch names

A branch that carries one published slice is named after that slice's tracker issue: `MIT-42-csv-export`. Linear links a pull request to an issue by the ID in its branch name. Every other branch is named after the work order: `WO-0004-csv-export`. That includes a branch carrying several slices, such as a cloud run that ends in one pull request; its pull request description names each published slice's issue (`Fixes MIT-42`, `Fixes MIT-43`) so the tracker still links them.

`work-breakdown` writes the resolved branch name into `PLAN.md`, and `ticket-publish` rewrites it if publishing changes it. The plan carries the name, not this rule, because the agent executing a plan may not have this plugin installed.

---

## These are defaults, not fixed paths

**Before writing anything, check the host project's `AGENTS.md` or `CLAUDE.md` for its own conventions.** If the project puts documentation somewhere else, follow the project. The layout above is what to use when the project says nothing.

This matters more than it looks. A skill that hardcodes a folder tree only works in repositories that already agreed to it.

---

## Reading legacy layouts

Work and documents written under earlier versions of this plugin are still valid. When looking for an input:

| Look here first | Then fall back to |
|---|---|
| `docs/work/WO-NNNN-<slug>/` | `docs/work/YYYY-MM-<slug>/` (version 3), then `docs/features/<slug>/` (version 1, which used `prd.md` for `requirements.md`) |
| `docs/domain/glossary.md`, `docs/domain/domain-model.md` | `docs/product/glossary.md`, `docs/product/domain-model.md` |
| `docs/architecture/decisions/`, `docs/product/decisions/` | `docs/decisions/<scope>/` |

If you find a document in a legacy location, say which one you are reading and carry on. **Do not migrate it unprompted.** Moving files out from under work that is in flight breaks the links in plans and tracker issues that already exist. Offer the user a list of `git mv` commands for a one-off migration instead, and let them run it.

New documents always use the current layout.

---

## The end of a transient document

`acceptance-review` closes a work order out. It moves the folder to `docs/work/_archive/WO-NNNN-<slug>/` and stamps every file:

```yaml
status: superseded
archived: YYYY-MM-DD
harvested_to: [docs/domain/glossary.md, docs/architecture/decisions/0007-webhook-retries.md]
```

It never deletes. Archived work is deleted by hand, once you are satisfied nothing was lost.

Nothing should link *into* `_archive/`. `AGENTS.md` entries point at live work folders and are removed when the work is accepted; plans and tracker issues link to durable documents, or to a live `PLAN.md`, never into the archive. An archived document that something still depends on was not finished being harvested.
