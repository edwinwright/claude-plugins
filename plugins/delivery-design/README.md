# delivery-design

A Claude plugin (Claude Code and Cowork) that takes an idea through product → feature → tickets → publish, with all source-of-truth documents co-located with the code.

> [!model] Mental Model
> - **One source of truth: local markdown files in the repo.** Linear and other tools are sync targets, never the source.
> - **Docs that explain the code live with the code.** Decision records, glossary, domain model, agent instructions — in-repo.
> - **A doc is worth writing only if its "why" can't be recovered from the code.** That single test gates every decision record.
> - **Don't invent formats that already have standards.** Agent context → `AGENTS.md`. Decisions → Decision Record.
> - **Skills handle procedures; `AGENTS.md` handles always-true rules.** Only put things in `AGENTS.md` that are short, always-true, and non-inferable from the code.

---

## The two workflows

```
PRODUCT workflow   (new product — bootstraps the early SDLC)
  idea
   ├─ product-brief   → docs/product/vision.md + docs/product/product-backlog.md
   ├─ domain-doc      → docs/product/glossary.md + docs/product/domain-model.md
   └─ (output)        → product-backlog.md (one entry per feature, MoSCoW prioritised)
                          │
                          ▼
FEATURE workflow   (per backlog item)
  feature-request → feature-brief → feature-design → feature-plan → feature-tickets → feature-publish
  intake            idea → PRD       PRD → spec       spec → plan     plan → files      files → Linear/GitHub
```

**New product:** run the product workflow, then push each backlog item through the feature workflow.
**New feature on an existing product:** skip straight to `feature-brief`.

---

## Skill roster

| Skill | Status | Notes |
|---|---|---|
| `feature-brief` | ships | idea → PRD |
| `feature-design` | ships | PRD → Tech Spec; emits/updates `AGENTS.md` |
| `feature-plan` | ships | PRD + Tech Spec → Delivery Plan |
| `feature-tickets` | ships | Delivery Plan → local ticket files |
| `feature-publish` | ships | local files → Linear or GitHub Issues |
| `decision-record` | ships | MADR-style; `architecture` / `product` / `process` scope; significance gate |
| `domain-doc` | ships | bootstraps and maintains `glossary.md` + `domain-model.md` |
| `product-brief` | ships | idea → Product Brief + `product-backlog.md` |
| `feature-request` | ships | intake triage; the front door before `feature-brief` |

---

## Output layout

```
AGENTS.md                          ← agent router (repo root — loaded automatically)
docs/
  product/
    vision.md                      ← product brief (product-brief)
    glossary.md                    ← ubiquitous language (domain-doc)
    domain-model.md                ← entities, relationships, invariants (domain-doc)
    product-backlog.md             ← MoSCoW feature list (product-brief)
  decisions/
    architecture/
      0001-<slug>.md
    product/
      0001-<slug>.md
    process/
      0001-<slug>.md
  features/
    <feature-name>/
      prd.md                       ← feature-brief
      tech-spec.md                 ← feature-design
      delivery-plan.md             ← feature-plan
      tickets/
        _epic.md                   ← feature-tickets
        <story-name>.md
```

---

## Install / use

This plugin installs via the repo's marketplace (see the repository root `README.md`):

```
/plugin marketplace add edwinwright/claude-plugins
/plugin install delivery-design
```

Skills trigger on natural language — see each skill's `SKILL.md` for trigger phrases.

---

## Design reference

The constraints this pipeline is built around — local markdown as the single source of truth, project-management tools as sync targets rather than origins, a significance gate on every decision record, and standard formats (MADR, `AGENTS.md`) over bespoke ones — are summarised in the repository [`README.md`](../../README.md), with the skill-authoring conventions in [`CLAUDE.md`](../../CLAUDE.md).
