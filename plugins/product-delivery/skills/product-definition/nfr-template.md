# Non-Functional Requirements

The bar every feature in this product meets unless it says otherwise. Feature documents reference this file and record only their **deltas** from it.

Each requirement has an ID, a number, and a way to check it. A requirement with no number is a wish — either give it a number or delete it. "Fast", "accessible", and "secure" are not requirements; they are adjectives.

---

## Performance

| ID | Requirement | How it is checked |
|---|---|---|
| PERF-1 | | |

## Accessibility

| ID | Requirement | How it is checked |
|---|---|---|
| A11Y-1 | | |

## Security & Privacy

| ID | Requirement | How it is checked |
|---|---|---|
| SEC-1 | | |

## Availability & Reliability

| ID | Requirement | How it is checked |
|---|---|---|
| REL-1 | | |

## Platform & Browser Support

| ID | Requirement | How it is checked |
|---|---|---|
| PLAT-1 | | |

## Observability

| ID | Requirement | How it is checked |
|---|---|---|
| OBS-1 | | |

## Compliance

Delete this section if nothing applies. An empty compliance section invites invention.

| ID | Requirement | How it is checked |
|---|---|---|
| COMP-1 | | |

---

## Changing this file

These are product-level commitments, so they change rarely and deliberately.

- **`acceptance-review` amends this file** when a delivered feature has genuinely moved the baseline — a new bar that now applies everywhere, not just to that feature.
- **A feature-specific requirement stays in the feature's requirements document** as a delta. It only graduates to this file when it becomes true of the whole product.
- **Tightening an existing number** is a decision worth recording. If it constrains future work in a way that is costly to undo, write a decision record.

A delta against this file is also what opens the non-functional escalation gate in `request-triage`. Keeping these numbers honest is what keeps that gate meaningful — if everything is a delta, nothing is.
