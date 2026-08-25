---
name: backlog-refinement
description: 'Refine a backlog item or triaged request into a requirements document (PRD): scope, users, functional requirements written as user stories, non-functional requirements, and what is explicitly out of scope. Use when a request has been triaged and needs specifying before technical design or work breakdown. Triggers: "write a PRD", "refine this backlog item", "backlog refinement", "write the requirements", "scope this feature", "what are the acceptance criteria". Not for refining prose or notes — this produces a delivery requirements document.'
---

# backlog-refinement

You are helping the user turn a product or feature idea into a structured Product Requirements Document (PRD).

## Routes

Runs on the **Standard** and **Full** routes. The Direct route skips it — on Direct, a requirements document would only restate the request. Routes are defined in `../request-triage/references/routes.md`.

**Before you start,** read the work order at `docs/work/YYYY-MM-<slug>/work-order.md` for the route and the triaged request. If no work order exists, say so and offer to run `request-triage` first, or to proceed on the Standard route without one. Do not invent a work order.

Your goal is a requirements document clear enough to hand on without further clarification — to `technical-design` on the Full route, or straight to `work-breakdown` on Standard.

---

## Step 1: Read the input

Read everything the user has provided — the idea description, any attached files, links, or context documents. Identify what is already clear and what is missing or ambiguous. Do not ask questions that are already answered by the input.

---

## Step 2: Check for product context

Check whether the user has shared product-level context — a product vision, `product-backlog.md`, `glossary.md`, or `domain-model.md`. If these files exist in the connected folder or have been attached, read them before proceeding.

If no product context is present and the idea sounds product-level rather than feature-level (e.g. "I want to build a new app from scratch"), tell the user:

> "This sounds like a new product rather than a feature on an existing one. Consider running `product-definition` first to define the product vision and backlog — then use `backlog-refinement` for each individual feature."

Otherwise proceed to Step 3.

---

## Step 3: Question mode

The goal here is to fill genuine gaps — not to run through a checklist. Ask only for what is missing or ambiguous after reading the input. Present questions as a numbered list so the user can answer all at once.

**Cap at 7 questions.** If the idea is so vague that you would need more than 7 questions to write a useful PRD, tell the user this and ask them to provide more context before continuing.

Draw from these as needed:

1. What product or codebase is this for?
2. Who is the primary user of this feature, and what problem does it solve for them?
3. What does success look like for this feature?
4. What is explicitly out of scope for this version?
5. How does this feature interact with or extend existing functionality?
6. Are there constraints from the existing product — technical, UX, or otherwise — that must be respected?
7. Can you share any relevant context — architecture notes, design system, API docs?

---

## Step 4: Write the PRD

Once you have enough information, write the requirements document using the template at `requirements-template.md`. Read that file before writing.

Fill every section. If a section genuinely has no content (for example, no known integrations), write "None identified at this stage" rather than leaving it blank. This signals deliberate intent rather than omission.

That rule is specific to pipeline artefacts. A PRD is a contract with `technical-design`, so an explicitly empty section carries information — it says the question was asked. Free-standing notes work the other way: `note-formats` deletes unused sections instead, because there is no downstream reader to reassure.

The **Open Questions** section matters — use it for anything that remains unresolved after the question mode. The `technical-design` skill will surface these before beginning technical work, so capturing them here keeps the pipeline clean.

---

## Step 5: Save and present

Save the requirements document into the work order's folder, alongside the work order that opened it:

```
docs/work/YYYY-MM-<slug>/requirements.md
```

Use the slug and date from the work order rather than deriving new ones — everything for one piece of work lives in one folder and moves together. If there is no work order, derive `YYYY-MM` from today and the slug from the feature name.

Placement and legacy layouts are covered in `../request-triage/references/artefacts.md`. Check the host project's `AGENTS.md` or `CLAUDE.md` first — `docs/work/` is the default, not a fixed path.

If no project folder is connected, save to the outputs folder as `[Feature Name] Requirements.md` and present the file.

Tell the user what comes next, which depends on the route:

> **Full route:** "This is ready to pass to `technical-design`, which will run a dev team of subagents to produce a Technical Specification."
>
> **Standard route:** "This is ready to pass to `work-breakdown`, which will decompose it into ticket files."

In both cases add:

> "If anything looks wrong or incomplete, edit it now — everything downstream is built on what this document says. If there are Open Questions, resolve them first."
