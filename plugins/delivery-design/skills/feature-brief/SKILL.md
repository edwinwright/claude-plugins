---
name: feature-brief
description: Write a Product Requirements Document (PRD) for a feature on an existing product. Use this skill when the user has a feature idea ready to scope — the product already exists and product-brief has been run (or is not needed). Trigger on "write a PRD", "feature requirements", "scope this feature", "new feature idea", or any time the user wants to formalise what a feature should do before handing it to the dev pipeline. For brand-new products with no prior product-brief, use product-brief first.
---

# feature-brief

You are helping the user turn a product or feature idea into a structured Product Requirements Document (PRD).

This is the second step in the feature pipeline:

1. **feature-request** — raw idea → triaged: scope now, or park in the backlog
2. **feature-brief** (this skill) — idea → PRD
3. **feature-design** — PRD → Technical Specification
4. **feature-plan** — PRD + Tech Spec → Delivery Plan
5. **feature-tickets** — Delivery Plan → work item tickets
6. **feature-publish** — ticket files → Linear or GitHub Issues

Your goal is a PRD that is clear, complete, and ready to hand to `feature-design` without further clarification.

---

## Step 1: Read the input

Read everything the user has provided — the idea description, any attached files, links, or context documents. Identify what is already clear and what is missing or ambiguous. Do not ask questions that are already answered by the input.

---

## Step 2: Check for product context

Check whether the user has shared product-level context — a product vision, `product-backlog.md`, `glossary.md`, or `domain-model.md`. If these files exist in the connected folder or have been attached, read them before proceeding.

If no product context is present and the idea sounds product-level rather than feature-level (e.g. "I want to build a new app from scratch"), tell the user:

> "This sounds like a new product rather than a feature on an existing one. Consider running `product-brief` first to define the product vision and backlog — then use `feature-brief` for each individual feature."

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

Once you have enough information, write the PRD using the template at `prd-template.md`. Read that file before writing.

Fill every section. If a section genuinely has no content (for example, no known integrations), write "None identified at this stage" rather than leaving it blank. This signals deliberate intent rather than omission.

That rule is specific to pipeline artefacts. A PRD is a contract with `feature-design`, so an explicitly empty section carries information — it says the question was asked. Free-standing notes work the other way: `note-formats` deletes unused sections instead, because there is no downstream reader to reassure.

The **Open Questions** section matters — use it for anything that remains unresolved after the question mode. The `feature-design` skill will surface these before beginning technical work, so capturing them here keeps the pipeline clean.

---

## Step 5: Save and present

Derive the feature slug from the feature name — lowercase, kebab-case (e.g. `user-authentication`, `billing-portal`).

If the user has a project folder connected, save the PRD to:

```
docs/features/<feature-slug>/prd.md
```

If no project folder is connected, save to the outputs folder as `[Feature Name] PRD.md` and present the file.

Tell the user:

> "This PRD is ready to pass to the `feature-design` skill, which will run a dev team of subagents to produce a Technical Specification. If anything looks wrong or incomplete, edit the PRD before continuing. If there are Open Questions, resolve them first."
