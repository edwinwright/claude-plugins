# [Feature Name] — Technical Specification

> **PRD:** [link or filename]
> **Date:** [date]
> **Status:** Draft

---

## Overview

Brief summary of what this spec covers and which PRD it implements. One paragraph.

---

## Architecture Decisions

### Tech Stack

| Layer | Technology | Notes |
|---|---|---|
| Frontend | | |
| Backend | | |
| Database | | |
| Infrastructure | | |
| Key services | | |

### System Components

| Component | Responsibility | New / Existing |
|---|---|---|
| | | |

### API Approach

[State the API style and reasoning.]

### Data Model Sketch

[High-level entities and relationships from the Architect.]

### Team Constraints

[Bulleted list of decisions the team must follow.]

---

## Frontend Design

### Component & Page Breakdown

[Pages, components, and their responsibilities.]

### State Management

[How state flows through the feature.]

### API Contract Requirements

[What the Frontend needs from the Backend — endpoints, request/response shapes.]

### Accessibility & Performance

Baseline: `docs/product/nfr.md`. Record only what this feature does *differently*, and how the frontend meets any delta the requirements document named.

[Delta, or "Baseline applies unchanged".]

---

## Backend Design

### API Design

[Endpoint definitions — route, method, auth, request, response, errors.]

### Data Model

[Detailed schema — tables/collections, fields, types, constraints, relationships.]

### Business Logic & Validation

[Service layer rules, validation, side effects.]

### Integrations

[Third-party or internal service calls.]

### Security

Baseline: `docs/product/nfr.md`. Cover what is specific to this feature — auth on these endpoints, validation of these inputs, sensitivity of this data — not the product-wide security posture.

[Feature-specific security design.]

### Error Handling

[Strategy for catching, logging, and surfacing errors.]

---

## Test Strategy

### Acceptance Test Scenarios

[Given / When / Then scenarios mapped to PRD user stories.]

### Integration Test Requirements

[API contract verification points.]

### Edge Cases & Risk Areas

[Boundary conditions, concurrency, permission edge cases, data integrity risks.]

### Test Coverage Plan

[Unit / integration / E2E / manual breakdown.]

### Infrastructure Gaps

[Missing fixtures, mocks, tooling, or environment config.]

---

## Open Questions

Anything unresolved that must be answered before or during development. Resolve these before passing to `work-breakdown`.

| # | Question | Raised by | Status |
|---|---|---|---|
| 1 | | | Open |

---

## Out of Technical Scope

Anything explicitly not covered in this spec that could be mistaken for in-scope.

-
