# delivery-design

A Claude plugin (Claude Code and Cowork) that takes work from an incoming request through to verified, accepted delivery — with the source-of-truth documents co-located with the code.

> [!model] Mental Model
> - **One source of truth: local markdown files in the repo.** Linear and other tools are sync targets, never the source.
> - **Docs that explain the code live with the code.** Decision records, glossary, domain model, agent instructions — in-repo.
> - **Durable and transient documents are kept apart.** A glossary describes how things *are*; a requirements document describes a change we *propose to make*. Mixing them is how a `docs/` directory stops being trusted.
> - **A doc is worth writing only if its "why" can't be recovered from the code.** That single test gates every decision record.
> - **Don't invent formats that already have standards.** Agent context → `AGENTS.md`. Decisions → MADR. Prioritisation → MoSCoW.
> - **The heaviest process must not be the default.** Small work takes a short route, and escalating requires naming a reason.

---

## The three routes

Every piece of work enters at `request-triage`, which decides whether it should be built and which route it takes.

```
A — Direct     request-triage → work-breakdown → build
                 a bug fix, a copy change — a requirements doc would only restate the request

B — Standard   request-triage → backlog-refinement → work-breakdown → build → acceptance-review
                 the default: a feature, days of work, a domain the team already understands

C — Full       requirements-discovery → product-definition → domain-modelling → request-triage →
               backlog-refinement → technical-design → delivery-planning →
               work-breakdown → build → acceptance-review
                 a new product, or work that trips an escalation gate
```

`build` is the coding work itself — not a skill, and nothing here orchestrates it.
`ticket-publish` is optional on any route. `decision-record` is available at any point.

### Escalation gates

Route B becomes Route C only when one of these is **actually** true, and the gate gets named in the work order:

- The work introduces domain concepts not already in `glossary.md`
- It changes behaviour across more than one bounded context
- It carries a real non-functional requirement — a numbered delta against `nfr.md`, not a wish
- It involves a third-party integration outside our control
- A decision inside it is costly or slow to reverse

An escalation without a named gate is a preference, not an escalation. Full definitions live in [`skills/request-triage/references/routes.md`](skills/request-triage/references/routes.md).

---

## Skill roster

| Skill | Routes | Does |
|---|---|---|
| `requirements-discovery` | C | Elicitation, stakeholder mapping, current-state assessment, problem framing |
| `product-definition` | C | Idea → vision, MoSCoW backlog, and the non-functional baseline |
| `domain-modelling` | any | Bootstraps and maintains `glossary.md` + `domain-model.md` |
| `request-triage` | all | The front door — build or park, and which route |
| `backlog-refinement` | B, C | Backlog item → requirements document |
| `technical-design` | C | Requirements → Technical Specification (virtual dev team of four lenses) |
| `delivery-planning` | C | Requirements + spec → sequenced Delivery Plan (four planning lenses) |
| `work-breakdown` | all | Specified work → local Epic + Story ticket files, plus the `AGENTS.md` entry |
| `ticket-publish` | optional | Local ticket files → Linear or GitHub Issues |
| `acceptance-review` | B, C | Verify against criteria, harvest what is durable, archive what is spent |
| `decision-record` | any | MADR-style record with a significance gate that refuses trivial ones |

---

## Output layout

Two kinds of document, kept apart on purpose.

```
AGENTS.md                          ← agent router (repo root — loaded automatically)
docs/
  product/                         DURABLE — present tense, true until the code changes
    vision.md                      ← product-definition
    product-backlog.md             ← product-definition; appended by request-triage
    glossary.md                    ← domain-modelling; appended by acceptance-review
    domain-model.md                ← domain-modelling; appended by acceptance-review
    nfr.md                         ← the non-functional baseline every feature inherits
  decisions/                       DURABLE
    architecture/0001-<slug>.md
    product/0001-<slug>.md
    process/0001-<slug>.md
  work/                            TRANSIENT — dated, prunable
    2026-08-csv-export/
      work-order.md                ← request-triage: route, gates, triaged request
      requirements.md              ← backlog-refinement
      tech-spec.md                 ← technical-design
      delivery-plan.md             ← delivery-planning
      tickets/
        _epic.md                   ← work-breakdown
        <story-name>.md
    _archive/
      2026-08-stripe-billing/      ← moved here by acceptance-review, stamped superseded
```

These are **defaults**, not fixed paths. Every skill checks the host project's `AGENTS.md` or `CLAUDE.md` first. See [`skills/request-triage/references/artefacts.md`](skills/request-triage/references/artefacts.md).

`requirements-discovery` is the exception: its outputs carry stakeholder names and commercial context, so it asks once where they should live and records the answer, rather than defaulting into the repo.

---

## What closes the loop

`acceptance-review` is the reason the rest holds together. When a work order is built it verifies the result against the criteria that were agreed, then moves what turned out to be *durable* — a business rule, an invariant, a term, a contested decision — into the glossary, the domain model, and the decision records. Then it archives the spent documents so they stop being read as current.

Without that step, everything learned during a build stays locked in a document describing a plan that no longer exists, and the durable set goes stale one feature at a time.

It never deletes. Archived work is stamped `status: superseded` with a `harvested_to` list, and you delete it by hand once satisfied.

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

The constraints this pipeline is built around are summarised in the repository [`README.md`](../../README.md), with the skill-authoring conventions in [`CLAUDE.md`](../../CLAUDE.md).
