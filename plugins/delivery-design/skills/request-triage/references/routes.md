# Delivery routes

The three paths a piece of work can take through this plugin. `request-triage` owns this decision and records it; every other skill reads it.

This file is the single source of truth for route definitions. No other skill restates them — they point here.

---

## The routes

```
A — Direct     request-triage → work-breakdown → build
B — Standard   request-triage → backlog-refinement → work-breakdown → build → acceptance-review
C — Full       requirements-discovery → product-definition → domain-modelling → request-triage →
               backlog-refinement → technical-design → delivery-planning →
               work-breakdown → build → acceptance-review
```

`build` is the coding work itself. It is not a skill in this plugin and nothing here orchestrates it.

`ticket-publish` is optional on every route — run it when the tickets should exist in Linear or GitHub, skip it when local files are enough. It is not a route step.

`decision-record` is available at any point on any route, whenever a costly, hard-to-reverse decision gets made.

---

## Choosing

**Route B is the default.** Start there and argue your way off it in one direction or the other.

### Take Route A when the requirements would only restate the request

A bug fix, a copy change, a config change, a version bump — work where writing a requirements document would produce a document that says nothing the original request did not. If you cannot name what a requirements document would add, there is nothing to add.

Route A skips `acceptance-review` because there is no requirements document to verify against and nothing durable to harvest. The tests and the code review are the acceptance.

### Take Route C only when you can name a gate

Route B becomes Route C when **any one** of these is actually true — not plausibly, not eventually:

| Gate | Actually true means |
|---|---|
| **New domain concepts** | The work introduces a term or entity that is not already in `docs/product/glossary.md`. Check the file; do not guess. |
| **Crosses bounded contexts** | It changes behaviour in more than one bounded context, so a decision on one side constrains the other. |
| **Real non-functional requirement** | It carries a numbered delta against `docs/product/nfr.md` — "p95 under 200ms", "WCAG 2.2 AA on this flow". A wish has no number and no delta, and does not open this gate. |
| **Third-party integration outside our control** | A service whose API, availability, or pricing we cannot change, and whose failure modes we have to design around. |
| **Costly or slow to reverse** | A decision inside the work touches a data model, a public API, a vendor commitment, a security model, or money. |

**Name the gate in the work order.** An escalation without a named gate is not an escalation, it is a preference — and the point of routing is that the full ceremony stops being the path of least resistance on small work.

If a gate is *probably* true but you cannot confirm it, say so and take Route B. Escalating later costs one extra skill run. Escalating pre-emptively costs four.

---

## Recording the decision

On any verdict that proceeds, `request-triage` writes a work order to `docs/work/YYYY-MM-<slug>/work-order.md`:

```yaml
---
route: direct | standard | full
escalation_gates: []        # required and non-empty when route is full
slug: <kebab-case>
opened: YYYY-MM-DD
status: open
---
```

Every downstream skill reads this file before doing anything else, and stamps `route` and `slug` into the frontmatter of whatever it writes.

The route lives in a file rather than being re-derived by each skill, because re-derivation fails across session boundaries and lets two skills reach different conclusions about the same work.

---

## Invoked out of order

Skills in this plugin are individually invocable and will be run out of sequence. The rule is the same everywhere:

1. **State what is missing.** Name the input you expected and where it normally comes from.
2. **Offer to proceed on what is available.** Most steps degrade usefully — `work-breakdown` can work from a requirements document without a delivery plan.
3. **Never fabricate the missing input.** Do not invent a requirements document to have something to read. Ask, or work from what is actually there and say which.

If no work order exists, do not create one from inside another skill — say so and offer to run `request-triage` first, or to proceed without one on the Standard route.

---

## Changing route mid-flight

Work escalates. When a gate turns out to be true after refinement has already run:

- Update `route` and `escalation_gates` in the work order.
- Run the skills the new route adds. Nothing already written is thrown away — a requirements document written on Route B is the same document Route C wants.

Work rarely de-escalates. If it does, leave the extra documents in place; `acceptance-review` archives them either way.
