# backlog-refinement

Turns a product or feature idea into a Product Requirements Document (PRD).

## When to use

Use this after `request-triage` has routed a request to Standard or Full.

Invoke it with phrases like:
- "I have an idea for..."
- "Write a PRD for..."
- "I want to build..."
- "Create a product brief for..."

## Inputs

| Input | Required | Notes |
|---|---|---|
| Idea description | Yes | Can be brief or detailed |
| Supporting files | No | Wireframes, screenshots, reference docs |
| Links | No | Competitor products, existing documentation |
| Architecture / context docs | No | Recommended for features on existing products |

## Output

A structured PRD saved as a Markdown file covering:

- Problem statement and goals
- User stories and functional requirements
- Non-functional requirements (performance, accessibility, security, platform)
- Scope (in and out)
- Assumptions, dependencies, and open questions

## How it works

1. Reads the input and identifies what is already clear
2. Asks whether this is a new product or a feature on an existing one
3. Asks targeted clarifying questions for any gaps (max 7)
4. Writes the requirements document using `requirements-template.md`
5. Saves the PRD and prompts the user to continue to `technical-design`

## Usage guidelines

This skill runs entirely within the main agent — no subagents are spawned.

| Setting | Recommendation |
|---|---|
| Model | **claude-sonnet-4-6** is sufficient for most ideas. Use **claude-opus-4-8** if the idea is complex, involves significant business logic, or you want richer user story generation. |
| Effort | **Low to medium.** The PRD is a structured writing task, not a reasoning-heavy one. |

The quality of the PRD depends heavily on what you provide upfront. The more context you give — business goals, constraints, target users — the fewer clarifying questions the skill will need to ask.

## Routes

Runs on the **Standard** and **Full** routes. Direct skips it: on Direct, a requirements document would only restate the request.

Routes, escalation gates, and the work order format are defined in [`request-triage/references/routes.md`](../request-triage/references/routes.md).

**Next step:** On Full, pass the requirements document to `technical-design`. On Standard, go straight to `work-breakdown`.

> If the PRD contains unresolved Open Questions, resolve them before passing to `technical-design`. The tech spec is built on top of what is stated here.

## Files

| File | Purpose |
|---|---|
| `SKILL.md` | Skill instructions |
| `README.md` | This file |
| `requirements-template.md` | Output template for the requirements document |
