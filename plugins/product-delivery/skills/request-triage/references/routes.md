# Delivery routes

The three paths a piece of work can take through this plugin. `request-triage` owns this decision and records it, as a checklist of steps in the work order; every other skill reads it.

This file is the single source of truth for route definitions and for the Steps each route writes. No other skill restates them — they point here.

---

## The routes

Every piece of work starts at `request-triage`.

```
A — Direct     request-triage → work-breakdown → build
B — Standard   request-triage → backlog-refinement → work-breakdown → build → acceptance-review
C — Full       request-triage → [domain-modelling] → backlog-refinement → technical-design →
               delivery-planning → work-breakdown → build → acceptance-review
C — Full, new product
               request-triage → [requirements-discovery] → product-definition → domain-modelling →
               request-triage for each Must Have
```

Steps in brackets run only when the work needs them; see **Steps** below.

`build` is the coding work itself. It is not a skill in this plugin and nothing here orchestrates it: the agent doing it follows the How to run section of the work order's `PLAN.md`.

`ticket-publish` is optional on every route: run it when the slices should exist in Linear or GitHub, skip it when the plan is enough. `decision-record` is available at any point on any route, whenever a costly, hard-to-reverse decision gets made. Neither is a route step, and neither appears in Steps unless the user adds it.

---

## Choosing

**Route B is the default.** Start there and argue your way off it in one direction or the other.

### Take Route A when the requirements would only restate the request

A bug fix, a copy change, a config change, a version bump — work where writing a requirements document would produce a document that says nothing the original request did not. If you cannot name what a requirements document would add, there is nothing to add.

With a tracker connected, a Route A request may go straight to the tracker as a single issue, with no work order at all: a bug that needs nothing more than an issue on a board. `request-triage` offers that choice; it is still where the work started.

Route A skips `acceptance-review` because there is no requirements document to verify against and nothing durable to harvest. The tests and the code review are the acceptance. Without that step nothing else archives the work folder, so on Route A the executing agent does it as the last instruction in `PLAN.md`'s How to run.

### Take Route C only when you can name a gate

Route B becomes Route C when **any one** of these is actually true — not plausibly, not eventually:

| Gate | Actually true means |
|---|---|
| **New domain concepts** | The work introduces a term or entity that is not already in `docs/domain/glossary.md`. Check the file; do not guess. |
| **Crosses bounded contexts** | It changes behaviour in more than one bounded context, so a decision on one side constrains the other. Check `docs/domain/context-map.md` where the project has one. |
| **Real non-functional requirement** | It carries a numbered delta against `docs/product/nfr.md` — "p95 under 200ms", "WCAG 2.2 AA on this flow". A wish has no number and no delta, and does not open this gate. |
| **Third-party integration outside our control** | A service whose API, availability, or pricing we cannot change, and whose failure modes we have to design around. |
| **Costly or slow to reverse** | A decision inside the work touches a data model, a public API, a vendor commitment, a security model, or money. |

**Name the gate in the work order.** An escalation without a named gate is not an escalation, it is a preference — and the point of routing is that the full ceremony stops being the path of least resistance on small work.

If a gate is *probably* true but you cannot confirm it, say so and take Route B. Escalating later costs one extra skill run. Escalating pre-emptively costs four.

### A new product is Route C by definition

When there is no `docs/product/vision.md` for the product, or the request is a whole product rather than a feature of one, the work order is a **product bootstrap**: `route: full`, `escalation_gates: [new product]`. It produces the durable product documents, not code, so it has no `PLAN.md` and no `acceptance-review`. Its features each come back through `request-triage` as work orders of their own.

---

## Recording the decision

On any verdict that proceeds, `request-triage` writes a work order to `docs/work/WO-NNNN-<slug>/work-order.md`. How the ID is chosen, and where the folder lives, is in `artefacts.md`.

```yaml
---
id: WO-NNNN                 # e.g. WO-0004, see artefacts.md
source:                     # tracker issue the request arrived as, e.g. MIT-42; else empty
route: direct | standard | full
escalation_gates: []        # required and non-empty when route is full
slug: <kebab-case>
opened: YYYY-MM-DD
status: open
---
```

Every downstream skill reads this file before doing anything else, and stamps `id`, `route` and `slug` into the frontmatter of whatever it writes.

The route lives in a file rather than being re-derived by each skill, because re-derivation fails across session boundaries and lets two skills reach different conclusions about the same work.

---

## Steps

The work order carries its route as a checklist, so nobody has to remember which skills apply or in what order. `request-triage` writes it from the lists below. One line per step, naming the skill and what it produces.

**Direct**

```markdown
## Steps
- [ ] work-breakdown → PLAN.md
- [ ] build (see PLAN.md, How to run)
```

**Standard**

```markdown
## Steps
- [ ] backlog-refinement → requirements.md
- [ ] work-breakdown → PLAN.md
- [ ] build (see PLAN.md, How to run)
- [ ] acceptance-review
```

**Full**

```markdown
## Steps
- [ ] domain-modelling → docs/domain/glossary.md, docs/domain/domain-model.md
- [ ] backlog-refinement → requirements.md
- [ ] technical-design → tech-spec.md
- [ ] delivery-planning → delivery-plan.md
- [ ] work-breakdown → PLAN.md
- [ ] build (see PLAN.md, How to run)
- [ ] acceptance-review
```

Include the `domain-modelling` line only when the new-domain-concepts gate is one of the gates named. Otherwise the glossary already covers the work.

**Full, new product**

```markdown
## Steps
- [ ] requirements-discovery → problem statement, stakeholder map, current state
- [ ] product-definition → docs/product/vision.md, product-backlog.md, nfr.md
- [ ] domain-modelling → docs/domain/glossary.md, docs/domain/domain-model.md
- [ ] request-triage for each Must Have backlog item (each becomes its own work order)
```

Include the `requirements-discovery` line only when the problem itself is not yet agreed: client work, or a request that arrives as a solution with no problem behind it. If the user already has a clear brief, start at `product-definition`.

The last line closes the bootstrap. The first time `request-triage` runs on one of the product's backlog items, it ticks that line, sets the bootstrap work order's `status: done`, and moves its folder to `docs/work/_archive/`.

---

## Following the Steps

Every skill that appears in a Steps list does the same three things.

1. **Before starting, read the work order.** If a line above yours is unticked, name it and ask whether to proceed without it. Most steps degrade usefully (`work-breakdown` can work from a requirements document without a delivery plan), but the user decides, not you.
2. **When finished, tick your own line** (`- [ ]` → `- [x]`). Change nothing else in the work order.
3. **End by naming the next unticked step**, as the last line of your hand-off: `Next: backlog-refinement`. If every line is ticked, say the work order is complete.

The `build` line is ticked by the agent executing `PLAN.md`, after the last slice's Check passes. `ticket-publish` and `decision-record` tick a line only if the user has added one for them; they never add one.

**Never fabricate a missing input.** Do not invent a requirements document to have something to read. Ask, or work from what is actually there and say which.

**If no work order exists,** do not create one from inside another skill. Say so and offer to run `request-triage` first, or to proceed without one on the Standard route. Without a work order there is nothing to tick; say that too, so the user knows the map is missing.

**Work orders from before Steps existed** have no `## Steps` section. Read the route and carry on; do not add one unless the user asks.

The definition lives here; each skill states the three things in its own `SKILL.md` as well, because a behaviour that has to happen on every run is not reliable as a pointer alone.

---

## Changing route mid-flight

Work escalates. When a gate turns out to be true after refinement has already run:

- Update `route` and `escalation_gates` in the work order.
- Add the lines the new route adds to Steps, unticked, in route order. Leave ticked lines ticked.
- Run the skills the new route adds. Nothing already written is thrown away — a requirements document written on Route B is the same document Route C wants.

Work rarely de-escalates. If it does, leave the extra documents in place and delete their unticked lines from Steps; `acceptance-review` archives the documents either way.
