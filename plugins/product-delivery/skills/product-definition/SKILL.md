---
name: product-definition
description: 'Define a new product: vision, target users, the problem it solves, success metrics, guiding principles, and a MoSCoW-prioritised feature backlog. Also used to revise an existing product definition. Outputs docs/product/vision.md and docs/product/product-backlog.md. Triggers: "I have an idea for a new product", "define the product", "write a product brief", "product vision", "what should I build", "I want to build an app", "write the product backlog".'
---

# product-definition

You are helping the user turn a product idea into a Product Brief, a product vision document, and an initial `product-backlog.md`.

## Routes

Runs at the head of the **Full** route, once per product rather than once per piece of work. It is also standalone — run it cold to revise an existing product definition. Routes are defined in `../request-triage/references/routes.md`.

**Before you start,** read `docs/discovery/` if it exists — `requirements-discovery` produces the problem statement, stakeholder map, and current-state assessment this skill turns into a vision and a backlog. If it does not exist, work from what the user gives you; do not invent stakeholders.

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

## Step 6: Write the non-functional baseline

Read the template at `nfr-template.md` and write `docs/product/nfr.md`.

This is the product-wide bar — the performance, accessibility, security, and availability requirements that every feature meets unless it states otherwise. Writing it once here is what stops each feature document restating them differently.

Two rules:

- **Every requirement needs a number and a way to check it.** "Fast" is not a requirement; "p95 page load under 2s on a 4G connection, measured by Lighthouse CI" is. If you cannot put a number on something, leave it out rather than writing an adjective.
- **Do not invent numbers the user has not given you.** Ask for the ones that matter and leave the rest as an explicit gap: `[not yet set — decide before the first release]`. A fabricated latency target is worse than a missing one, because it looks like a decision.

Where the input is silent, ask. Cap this at three questions on top of the seven in Step 3 — accessibility standard, browser support, and whichever of performance or compliance the product most obviously depends on.

---

## Step 7: Save and present

Save the Product Brief to `docs/product/vision.md`, the backlog to `docs/product/product-backlog.md`, and the baseline to `docs/product/nfr.md`.

These are the default paths. Check the host project's `AGENTS.md` or `CLAUDE.md` first — see `../request-triage/references/artefacts.md`.

Present all three files to the user.

Tell the user:

> "The product brief, backlog, and non-functional baseline are ready. The recommended next steps are:
>
> 1. Run **`domain-modelling`** to bootstrap the glossary and domain model — this gives the pipeline the shared vocabulary it needs, and it is what the first escalation gate is checked against.
> 2. Then run **`request-triage`** on each backlog item, starting with the Must Haves.
>
> If anything in the brief looks wrong, edit it now — the backlog and everything downstream is built on top of these decisions. Any gaps left in `nfr.md` are worth closing before the first feature ships; features inherit that file whether or not it is finished."
