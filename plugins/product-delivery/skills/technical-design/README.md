# technical-design

Transforms a Product Requirements Document (PRD) into a Technical Specification by running a virtual dev team of specialist subagents.

## When to use

Use this after `backlog-refinement` has produced a requirements document, on work that triage routed to Full.

Do not run this skill if the PRD has unresolved Open Questions — resolve them first. The tech spec is built on top of what the PRD states.

## Inputs

| Input | Required | Notes |
|---|---|---|
| PRD | Yes | From `backlog-refinement`, as a file or pasted content |
| Tech stack constraints | No | Paste inline or attach a doc. If omitted, the Architect recommends a stack. |

## Output

A Technical Specification saved as a Markdown file, covering:

- Architecture decisions (stack, components, API approach, data model)
- Frontend design (components, state, API contract requirements)
- Backend design (API, data model, business logic, security, error handling)
- Test strategy (acceptance scenarios, integration tests, edge cases)
- Consolidated open questions

## How it works

The skill runs four subagents in sequence, then synthesises their outputs:

```
Architect
    ↓ (architecture decisions)
Frontend Engineer + Backend Engineer  ← run in parallel
    ↓ (FE spec + BE spec)
QA Engineer
    ↓ (test strategy)
Main agent synthesises → Technical Specification
```

1. **Architect** — reads the PRD and tech constraints; decides stack, components, API style, and data model sketch; sets constraints for the rest of the team
2. **Frontend Engineer** — designs components, state management, and defines the API contract the frontend needs
3. **Backend Engineer** — designs the API, data schema, business logic, and security (runs in parallel with Frontend)
4. **QA Engineer** — writes acceptance scenarios, integration test requirements, and edge cases from all prior outputs
5. **Main agent** — consolidates everything into a single Technical Specification, resolves FE/BE conflicts, surfaces open questions

## Usage guidelines

This skill is the most resource-intensive step in the pipeline — five agents run in sequence. Expect it to take several minutes.

| Agent | Model | Effort | Reason |
|---|---|---|---|
| Architect | claude-sonnet-4-6 | High | Sets hard constraints for the entire team — thoroughness matters here |
| Frontend Engineer | claude-sonnet-4-6 | Medium | Structured spec work; Architect's decisions reduce ambiguity |
| Backend Engineer | claude-sonnet-4-6 | Medium | As above |
| QA Engineer | claude-sonnet-4-6 | Medium | Derives tests from prior outputs; no novel reasoning required |

The **main orchestrating agent** (the session you run the skill in) handles coordination and synthesis. **claude-sonnet-4-6** is recommended for the session model — the heavy lifting is done by the subagents.

If you have a large or architecturally complex PRD, consider upgrading the Architect subagent to **claude-opus-4-8** for richer decision-making. The SKILL.md controls this — edit the Architect step if needed.

## Routes

Runs on the **Full** route only. Direct and Standard skip it — a feature in a well-understood domain, with no escalation gate open, does not need a technical specification to be broken into stories.

Routes, escalation gates, and the work order format are defined in [`request-triage/references/routes.md`](../request-triage/references/routes.md).

**Next step:** Pass the saved Tech Spec to `delivery-planning`.

> If the Tech Spec contains unresolved Open Questions, resolve them before continuing. They will affect how delivery is sequenced.

## Files

| File | Purpose |
|---|---|
| `SKILL.md` | Orchestration instructions |
| `README.md` | This file |
| `tech-spec-template.md` | Output template for the Technical Specification |
| `prompts/architect.md` | Architect subagent instructions |
| `prompts/frontend.md` | Frontend Engineer subagent instructions |
| `prompts/backend.md` | Backend Engineer subagent instructions |
| `prompts/qa.md` | QA Engineer subagent instructions |
