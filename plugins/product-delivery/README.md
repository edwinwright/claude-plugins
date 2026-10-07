# product-delivery

**Every piece of work starts with `request-triage`.** It writes a work order whose `## Steps` checklist names the skills to run, in order. Each skill ticks its own line and names the next, so the route never has to be remembered.

A Claude plugin (Claude Code and Cowork) that takes work from an incoming request through to verified, accepted delivery, with the source-of-truth documents co-located with the code.

> [!model] Mental Model
> - **Markdown in the repo is the source of truth for content; the tracker, when used, owns IDs and status.** Linear and GitHub Issues mirror the plan; they never originate it.
> - **The work order is the map.** Its Steps list says which skills apply and what comes next.
> - **Docs that explain the code live with the code.** Decision records, glossary, domain model, agent instructions: in-repo.
> - **Durable and transient documents are kept apart.** A glossary describes how things *are*; a requirements document describes a change we *propose to make*. Mixing them is how a `docs/` directory stops being trusted.
> - **A doc is worth writing only if its "why" can't be recovered from the code.** That single test gates every decision record.
> - **Don't invent formats that already have standards.** Agent context → `AGENTS.md`. Decisions → MADR. Prioritisation → MoSCoW.
> - **The heaviest process must not be the default.** Small work takes a short route, and escalating requires naming a reason.

---

## The three routes

`request-triage` decides whether the work should be built and which route it takes, then writes the route into the work order as Steps.

```
A — Direct     request-triage → work-breakdown → build
                 a bug fix, a copy change: a requirements doc would only restate the request

B — Standard   request-triage → backlog-refinement → work-breakdown → build → acceptance-review
                 the default: a feature, days of work, a domain the team already understands

C — Full       request-triage → [domain-modelling] → backlog-refinement → technical-design →
               delivery-planning → work-breakdown → build → acceptance-review
                 work that trips an escalation gate

C — Full, new product
               request-triage → [requirements-discovery] → product-definition → domain-modelling →
               request-triage for each Must Have
                 a product that does not exist yet
```

Bracketed steps run only when the work needs them, and `request-triage` leaves them out of Steps otherwise. `build` is the coding work itself: an agent executes the work order's `PLAN.md` one slice at a time. `ticket-publish` is optional on any route; `decision-record` is available at any point. Neither appears in Steps unless you add it.

A Standard work order looks like this:

```markdown
## Steps
- [x] backlog-refinement → requirements.md
- [ ] work-breakdown → PLAN.md
- [ ] build (see PLAN.md, How to run)
- [ ] acceptance-review
```

Run a skill out of order and it names the unticked step above it and asks whether to go ahead. Ask `request-triage` "what's next on MD-123" and it reports the list without re-triaging.

### Escalation gates

Route B becomes Route C only when one of these is **actually** true, and the gate gets named in the work order:

- The work introduces domain concepts not already in `glossary.md`
- It changes behaviour across more than one bounded context
- It carries a real non-functional requirement: a numbered delta against `nfr.md`, not a wish
- It involves a third-party integration outside our control
- A decision inside it is costly or slow to reverse

An escalation without a named gate is a preference, not an escalation. Full definitions, and every route's Steps, live in [`skills/request-triage/references/routes.md`](skills/request-triage/references/routes.md).

---

## Skill roster

| Skill | Routes | Does |
|---|---|---|
| `request-triage` | all | The front door: build or park, which route, the work order and its Steps |
| `requirements-discovery` | C, new product | Elicitation, stakeholder mapping, current-state assessment, problem framing |
| `product-definition` | C, new product | Idea → vision, MoSCoW backlog, and the non-functional baseline |
| `domain-modelling` | any | Bootstraps and maintains `glossary.md` + `domain-model.md` |
| `backlog-refinement` | B, C | Backlog item → requirements document |
| `technical-design` | C | Requirements → Technical Specification (virtual dev team of four lenses) |
| `delivery-planning` | C | Requirements + spec → sequenced Delivery Plan (four planning lenses) |
| `work-breakdown` | all | Specified work → `PLAN.md` of vertical slices, plus the `AGENTS.md` entry |
| `ticket-publish` | optional | `PLAN.md` slices → Linear or GitHub Issues, IDs written back |
| `acceptance-review` | B, C | Verify against criteria, harvest what is durable, archive what is spent |
| `decision-record` | any | MADR-style record with a significance gate that refuses trivial ones |

---

## The plan

`work-breakdown` writes one `PLAN.md` per work order: numbered slices, ordered by dependency, each a vertical change that leaves the build green. Each slice carries its files, what not to touch, its tasks, acceptance criteria with a `Verify:` line each, and a Check block of exact commands.

The plan opens with **How to run this**, the rules the executing agent follows: one slice at a time; run the slice's Check before stopping; if a Check fails twice, stop and report; never improvise a substitute or skip ahead. A slice marked `review: stop` waits for you to review the diff and commit; `review: continue` commits and carries on. A cloud or background agent treats every slice as `continue` and ends with one pull request, so cloud work is sized to one PR.

Each slice fits one agent session. Past about eight slices, `work-breakdown` recommends splitting the work order.

---

## Output layout

```
AGENTS.md                         agent router (CLAUDE.md symlinks to it)
docs/
  architecture/                   DURABLE: the technical lens
    tech-stack.md                 read by technical-design
    conventions.md                read by technical-design
    decisions/                    ← decision-record (architecture, process)
  domain/                         DURABLE: shared ground
    glossary.md                   ← domain-modelling; appended by acceptance-review
    domain-model.md               ← domain-modelling; appended by acceptance-review
    context-map.md                optional, hand-written
  product/                        DURABLE: the value lens
    vision.md                     ← product-definition
    product-backlog.md            ← product-definition; appended by request-triage
    nfr.md                        ← product-definition; the baseline every feature inherits
    decisions/                    ← decision-record (product)
  work/                           TRANSIENT, committed
    MD-123-csv-export/            one folder per work order: <ID>-<slug>
      work-order.md               ← request-triage: route, gates, Steps
      requirements.md             ← backlog-refinement (B, C)
      tech-spec.md                ← technical-design (C)
      delivery-plan.md            ← delivery-planning (C)
      PLAN.md                     ← work-breakdown (all routes)
    _archive/
      MD-117-stripe-billing/      ← moved here by acceptance-review, stamped superseded
```

The ID follows the project's ticketing prefix. With a tracker connected it is the tracker's issue ID; without one, the prefix declared in `AGENTS.md` and the next free number. If neither exists, `request-triage` asks once and tells you to record the prefix in `AGENTS.md`.

Work folders are committed so cloud agents can read them.

These are **defaults**, not fixed paths. Every skill checks the host project's `AGENTS.md` or `CLAUDE.md` first. Projects on the version 3 layout (`docs/work/YYYY-MM-<slug>/`, `docs/product/glossary.md`, `docs/decisions/`) are still read; nothing is migrated without you. See [`skills/request-triage/references/artefacts.md`](skills/request-triage/references/artefacts.md).

`requirements-discovery` is the exception: its outputs carry stakeholder names and commercial context, so it asks once where they should live and records the answer, rather than defaulting into the repo.

---

## What closes the loop

`acceptance-review` is the reason the rest holds together. When a work order is built it verifies the result against the criteria that were agreed, then moves what turned out to be *durable* (a business rule, an invariant, a term, a contested decision) into the glossary, the domain model, and the decision records. Then it archives the spent documents so they stop being read as current, and ticks the last line of Steps.

Without that step, everything learned during a build stays locked in a document describing a plan that no longer exists, and the durable set goes stale one feature at a time.

It never deletes. Archived work is stamped `status: superseded` with a `harvested_to` list, and you delete it by hand once satisfied. On Route A, which has nothing to harvest, the agent executing the plan archives the folder itself as its last step.

---

## Install / use

This plugin installs via the repo's marketplace (see the repository root `README.md`):

```
/plugin marketplace add edwinwright/claude-plugins
/plugin install product-delivery
```

Skills trigger on natural language; see each skill's `SKILL.md` for trigger phrases.

---

## Design reference

The constraints this pipeline is built around are summarised in the repository [`README.md`](../../README.md), with the skill-authoring conventions in [`CLAUDE.md`](../../CLAUDE.md).
