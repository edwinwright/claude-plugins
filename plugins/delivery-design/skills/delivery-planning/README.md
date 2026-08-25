# delivery-planning

Transforms a PRD and Technical Specification into a dependency-aware Delivery Plan by running a virtual planning team of specialist subagents.

## When to use

Use this after `technical-design` has produced a Technical Specification. It is step three of the feature pipeline:

```
backlog-refinement → technical-design → delivery-planning → work-breakdown
```

Do not run this skill if the Tech Spec has unresolved Open Questions — anything that affects scope or architecture will produce incorrectly sequenced stories.

## Inputs

| Input | Required | Notes |
|---|---|---|
| Technical Specification | Yes | From `technical-design`, as a file or pasted content |
| PRD | No | Recommended — provides user-facing context the Tech Spec may compress |

## Output

A Delivery Plan saved as a Markdown file, covering:

- Stories grouped into phases, each representing a deployable slice of the system
- Story type for each item (Story or Technical)
- Explicit dependencies between stories
- Full story list table for handoff to `work-breakdown`
- Open questions that would affect sequencing

## How it works

The skill runs four planning subagents in parallel, then synthesises their outputs:

```
Backend Planner + Frontend Planner + QA Planner + DevOps Planner  ← run in parallel
    ↓
Main agent synthesises → Delivery Plan
```

1. **Backend Planner** — sequences backend work: schema migrations, API endpoints, business logic, and service dependencies
2. **Frontend Planner** — sequences frontend work: components, state, routing, and API integration points
3. **QA Planner** — identifies test infrastructure needs and where test coverage gates phase completion
4. **DevOps Planner** — identifies foundational infrastructure work (CI, hosting, secrets, third-party provisioning) that must exist before feature stories can begin
5. **Main agent** — resolves cross-discipline conflicts, enforces vertical slices, assigns phase boundaries, and presents the plan for review before saving

The user reviews the phase and story breakdown before the plan is saved.

## Usage guidelines

The four planning subagents all run in parallel, so the wall-clock time is similar to a single subagent run.

| Agent | Model | Effort |
|---|---|---|
| Backend Planner | claude-sonnet-4-6 | Medium |
| Frontend Planner | claude-sonnet-4-6 | Medium |
| QA Planner | claude-sonnet-4-6 | Medium |
| DevOps Planner | claude-sonnet-4-6 | Medium |

The **main orchestrating agent** handles synthesis and conflict resolution. **claude-sonnet-4-6** is recommended for the session model.

The quality of the plan depends on the Tech Spec. A sparse or ambiguous spec produces a plan with more open questions and vaguer phase boundaries. Resolve Tech Spec gaps before running this skill.

## Pipeline

```
backlog-refinement     →     technical-design     →     delivery-planning     →     work-breakdown
idea → PRD              PRD → Tech Spec          Tech Spec → Plan        Plan → tickets
```

**Next step:** Pass the saved Delivery Plan (and Tech Spec if available) to `work-breakdown`.

> If the Delivery Plan contains unresolved Open Questions, resolve them before continuing. They will affect ticket scope and phase assignment.

## Files

| File | Purpose |
|---|---|
| `SKILL.md` | Orchestration instructions |
| `README.md` | This file |
| `delivery-plan-template.md` | Output template for the Delivery Plan |
| `prompts/backend-planner.md` | Backend Planner subagent instructions |
| `prompts/frontend-planner.md` | Frontend Planner subagent instructions |
| `prompts/qa-planner.md` | QA Planner subagent instructions |
| `prompts/devops-planner.md` | DevOps Planner subagent instructions |
