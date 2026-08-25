---
name: technical-design
description: 'Produce a Technical Specification from a requirements document by running a virtual dev team of specialist subagents — architect, frontend, backend, and QA. Settles stack, components, API contracts, data model, security, and test strategy before delivery is sequenced. Triggers: "create a tech spec", "technical design for", "run the dev team on this", "how should we build this technically", "turn this PRD into a spec", "what is the architecture for this feature".'
---

# technical-design

You are orchestrating a virtual dev team to transform a PRD into a Technical Specification.

## Routes

Runs on the **Full** route only. Direct and Standard skip it — a feature in a well-understood domain, with no escalation gate open, does not need a technical specification to be broken into stories. Routes are defined in `../request-triage/references/routes.md`.

**Before you start,** read the work order at `docs/work/YYYY-MM-<slug>/work-order.md`. If it says `route: standard` or `route: direct`, ask whether a gate has opened since triage — if one has, update the work order and continue; if not, say the work does not need this step and stop.

The output feeds directly into `delivery-planning`, so it must be complete and internally consistent.

---

## Step 1: Receive and validate the PRD

Accept the PRD as a file, pasted content, or linked note. Read it in full.

Check the **Open Questions** section. If it contains unresolved questions, surface them to the user and ask for answers before continuing. A tech spec built on unresolved product questions will produce unreliable delivery plans and tickets.

---

## Step 2: Establish tech stack constraints

Ask the user:

> **Do you have tech stack constraints for this feature?**
> If yes, paste them here or attach a tech doc (e.g. architecture notes, README, tech stack reference).
> If no constraints, the Architect will recommend a stack.

If constraints are provided, keep them accessible — you will pass them to the Architect subagent.

---

## Step 3: Run the Architect subagent

Read the file `prompts/architect.md`. Spawn an anonymous subagent via the Agent tool using the file contents as the system prompt. Do not use any globally registered agent — create a fresh subagent with these exact instructions.

Configure the subagent with:
- model: claude-sonnet-4-6
- effort: high

Pass the subagent:
- The full PRD content
- Any tech stack constraints provided by the user

Wait for the Architect to complete. Save its output as `_architect-output.md` in the working directory — subsequent subagents depend on it.

---

## Step 4: Run Frontend and Backend subagents in parallel

Read `prompts/frontend.md` and `prompts/backend.md`. Spawn both as anonymous subagents in the same turn via the Agent tool, using each file's contents as the system prompt. Do not use any globally registered agent — create fresh subagents with these exact instructions.

Configure each subagent with:
- model: claude-sonnet-4-6
- effort: medium

Each subagent receives:
- The full PRD content
- The Architect output (`_architect-output.md`)

They must treat the Architect's decisions as hard constraints, not suggestions.

Wait for both to complete. Save their outputs as:
- `_frontend-output.md`
- `_backend-output.md`

---

## Step 5: Run the QA subagent

Read `prompts/qa.md`. Spawn an anonymous subagent via the Agent tool using the file contents as the system prompt. Do not use any globally registered agent — create a fresh subagent with these exact instructions.

Configure the subagent with:
- model: claude-sonnet-4-6
- effort: medium

Pass the subagent:
- The full PRD content
- `_architect-output.md`
- `_frontend-output.md`
- `_backend-output.md`

Wait for the QA subagent to complete. Save its output as `_qa-output.md`.

---

## Step 6: Synthesise the Technical Specification

Read the template at `tech-spec-template.md`. Combine all four subagent outputs into a single, coherent Technical Specification.

During synthesis:

- **Resolve conflicts** between the Frontend and Backend outputs where possible (e.g. mismatched API contract expectations). If a conflict cannot be resolved without product input, add it to the Open Questions section rather than silently picking one side.
- **Preserve all open questions** raised by any subagent — consolidate them into one list.
- **Do not invent content.** If a subagent left a section sparse, carry that sparsity through rather than filling it with assumptions.

---

## Step 7: Save the Technical Specification

Derive the feature slug from the feature name — lowercase, kebab-case (e.g. `user-authentication`, `billing-portal`).

If the user has a project folder connected, save the Technical Specification to:

```
docs/features/<feature-slug>/tech-spec.md
```

If no project folder is connected, save as `[Feature Name] Tech Spec.md` in the working directory.

Clean up the intermediate files (`_architect-output.md`, `_frontend-output.md`, `_backend-output.md`, `_qa-output.md`) from the working directory once the final spec is saved.

---

## Step 8: Emit or update AGENTS.md

**Guardrail: keep AGENTS.md lean.** Research is clear that an over-stuffed AGENTS.md reduces agent task success and adds cost. This step adds only what is non-inferable: a pointer to this feature's docs. Do not add procedures, style guides, or explanations — those belong in skills or linked docs.

Check whether an `AGENTS.md` exists at the repo root.

**If it does not exist:** read the template at `agents-template.md`. Create `AGENTS.md` at the repo root using the template. Fill in:
- The "What this is" line from `docs/product/vision.md` if it exists, or a one-line summary from the PRD
- A "Before you start" entry for this feature pointing to its `prd.md` and `tech-spec.md`
- Build/test/run commands if they are known from the tech spec

**If it already exists:** read it. Append or update only the "Before you start" entry for this feature. Do not modify any other section. Do not add prose. One line per feature pointing to its two docs.

Example entry to add or update:
```
- Implementing [Feature Name] → docs/features/<feature-slug>/prd.md + docs/features/<feature-slug>/tech-spec.md
```

Present the AGENTS.md change to the user and tell them:

> "AGENTS.md updated with a pointer to this feature's docs. Keep this file to roughly one screen — if it grows past that, the overflow belongs in a skill or a linked doc, not here."

---

## Step 9: Present and hand off

Present the Technical Specification and the AGENTS.md change to the user.

Tell the user:

> "The Technical Specification is ready to pass to the `delivery-planning` skill, which will run a planning team of subagents to sequence the work into a Delivery Plan. If there are Open Questions in the spec, resolve them before continuing — they will affect how delivery is sequenced."
