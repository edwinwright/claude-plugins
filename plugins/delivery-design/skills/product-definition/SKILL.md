---
name: product-definition
description: 'Define a new product: vision, target users, the problem it solves, success metrics, guiding principles, and a MoSCoW-prioritised feature backlog. Also used to revise an existing product definition. Outputs docs/product/vision.md and docs/product/product-backlog.md. Triggers: "I have an idea for a new product", "define the product", "write a product brief", "product vision", "what should I build", "I want to build an app", "write the product backlog".'
---

# product-definition

You are helping the user turn a product idea into a Product Brief, a product vision document, and an initial `product-backlog.md`.

This is the first step in the product workflow:

1. **product-definition** (this skill) — idea → Product Brief / Vision + `product-backlog.md`
2. **domain-modelling** — bootstraps `glossary.md` + `domain-model.md`
3. **request-triage** — triages each new idea against the backlog before it is scoped
4. **backlog-refinement** — one PRD per backlog item → feeds the feature pipeline

Your goal is a Product Brief clear enough to give a developer the "why" of the product, and a `product-backlog.md` rich enough for `backlog-refinement` to open any backlog item without further context.

---

## Step 1: Read the input

Read everything the user has provided — the product idea, any attached context, links, or prior thinking. Identify what is already clear and what is missing or ambiguous. Do not ask questions that are already answered by the input.

---

## Step 2: Check for existing product context

Check whether any product context already exists — a prior `docs/product/vision.md`, a backlog, or a glossary. If they exist, read them before proceeding. This skill can be used to revise an existing brief, not just create a new one.

---

## Step 3: Question mode

Ask only for what is missing or ambiguous after reading the input. Present questions as a numbered list so the user can answer all at once.

**Cap at 7 questions.** If the idea is so vague that you would need more than 7 questions to write a useful brief, tell the user and ask them to provide more context first.

Draw from these as needed:

1. Who is the primary target user, and what problem does this product solve for them?
2. What does success look like in three to six months — what would the user be doing differently?
3. What is explicitly out of scope for the first version?
4. Are there competitors or prior art? What do you do differently or better?
5. What is the distribution model — how will users find and access this product?
6. Are there any hard technical, regulatory, or commercial constraints?
7. Are there any non-negotiable principles that must shape every product decision?

---

## Step 4: Write the Product Brief

Read the template at `vision-template.md`. Fill every section.

If a section genuinely has no content, write "None identified at this stage" rather than leaving it blank. This signals deliberate intent rather than omission.

That rule is specific to pipeline artefacts. A Product Brief is a contract with the next stage, so an explicitly empty section carries information — it says the question was asked. Free-standing notes work the other way: `note-formats` deletes unused sections instead, because there is no downstream reader to reassure.

---

## Step 5: Write the product backlog

Read the template at `product-backlog-template.md`. Write `docs/product/product-backlog.md`.

Structure the backlog as a MoSCoW-prioritised list. For each item:

- **Title** — the feature name (this will become the `backlog-refinement` prompt)
- **User value** — one sentence: what the user can do and why it matters
- **Scope note** *(optional)* — any constraint or boundary whoever refines this item must know

The backlog must be sufficient for `backlog-refinement` to open any item without further context from you. Each entry should stand alone.

Typical backlog size for a first brief: 6–15 items across MoSCoW tiers. Do not invent features not supported by the input — if the brief is genuinely minimal, the backlog will be short.

---

## Step 6: Save and present

Save the Product Brief to `docs/product/vision.md`. Save the product backlog to `docs/product/product-backlog.md`.

Present both files to the user.

Tell the user:

> "The product brief and backlog are ready. The recommended next steps are:
>
> 1. Run **`domain-modelling`** to bootstrap the glossary and domain model — this gives the feature pipeline the shared vocabulary it needs.
> 2. Then run **`backlog-refinement`** for each backlog item, starting with the Must Haves.
>
> If anything in the brief looks wrong, edit it now — the backlog and all downstream docs are built on top of these decisions."
