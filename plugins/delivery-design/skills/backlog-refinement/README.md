# backlog-refinement

Turns a product or feature idea into a Product Requirements Document (PRD).

## When to use

Use this at the start of any new feature or product. It is step one of the feature pipeline:

```
backlog-refinement → technical-design → delivery-planning → work-breakdown
```

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
4. Writes the PRD using `prd-template.md`
5. Saves the PRD and prompts the user to continue to `technical-design`

## Usage guidelines

This skill runs entirely within the main agent — no subagents are spawned.

| Setting | Recommendation |
|---|---|
| Model | **claude-sonnet-4-6** is sufficient for most ideas. Use **claude-opus-4-8** if the idea is complex, involves significant business logic, or you want richer user story generation. |
| Effort | **Low to medium.** The PRD is a structured writing task, not a reasoning-heavy one. |

The quality of the PRD depends heavily on what you provide upfront. The more context you give — business goals, constraints, target users — the fewer clarifying questions the skill will need to ask.

## Pipeline

```
backlog-refinement     →     technical-design     →     delivery-planning     →     work-breakdown
idea → PRD              PRD → Tech Spec          Tech Spec → Plan        Plan → tickets
```

**Next step:** Pass the saved PRD to `technical-design`.

> If the PRD contains unresolved Open Questions, resolve them before passing to `technical-design`. The tech spec is built on top of what is stated here.

## Files

| File | Purpose |
|---|---|
| `SKILL.md` | Skill instructions |
| `README.md` | This file |
| `prd-template.md` | Output template used when writing the PRD |
