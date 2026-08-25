---
name: delivery-planning
description: 'Sequence a specified feature into dependency-ordered, independently deployable phases by running a virtual planning team of specialist subagents. Produces a Delivery Plan naming what gets built first, what blocks what, and what a tester can verify at the end of each phase. Use once a Technical Specification exists and before tickets are written. Triggers: "plan the delivery", "sequence this work", "what order do we build things in", "break this into phases", "what gets built first", "delivery plan".'
---

# delivery-planning

You are orchestrating a virtual planning team to transform a PRD and Technical Specification into a Delivery Plan.

## Routes

Runs on the **Full** route only. Direct and Standard skip it — `work-breakdown` derives its own ordering from dependencies when there is no delivery plan. Routes are defined in `../request-triage/references/routes.md`.

**Before you start,** read the work order at `docs/work/YYYY-MM-<slug>/work-order.md`. If it says `route: standard` or `route: direct`, say the work does not need this step and stop, unless the user tells you a gate has opened since triage.

The goal is a sequenced, dependency-aware plan that answers: *what gets built first, what depends on what, and how do we ship a working, testable slice of the system at each phase?*

The output of this skill feeds directly into `work-breakdown`. Every story in the Delivery Plan must be independently deployable and testable — this is the primary quality check of the plan.

---

## Step 1: Receive and validate the inputs

Accept the Technical Specification as a file, pasted content, or linked note. If a PRD is also available, read it — it provides the user-facing context that the Tech Spec may compress.

Check the **Open Questions** section of the Tech Spec. If it contains unresolved questions that would affect sequencing (e.g. "which auth approach will we use?", "will payments be handled in v1?"), surface them to the user and ask for answers before continuing. Unresolved questions produce incorrectly sequenced plans.

---

## Step 2: Run the planning subagents in parallel

Read the following prompt files and spawn all four as anonymous subagents **in the same turn** via the Agent tool, using each file's contents as the system prompt. Do not use any globally registered agent — create fresh subagents with these exact instructions.

Configure each subagent with:
- model: claude-sonnet-4-6
- effort: medium

- `prompts/backend-planner.md` → Backend Planner
- `prompts/frontend-planner.md` → Frontend Planner
- `prompts/qa-planner.md` → QA Planner
- `prompts/devops-planner.md` → DevOps Planner

Each subagent receives:
- The full Tech Spec content
- The PRD content (if available)

Wait for all four to complete. Save their outputs as:
- `_backend-plan.md`
- `_frontend-plan.md`
- `_qa-plan.md`
- `_devops-plan.md`

---

## Step 3: Synthesise the Delivery Plan

Read the template at `delivery-plan-template.md`. Combine all four subagent outputs into a single, coherent Delivery Plan.

During synthesis, apply these principles:

**Resolve conflicts.** If Backend says the auth service must exist before anything else, but Frontend has put auth UI in Phase 2, reconcile this — the Backend constraint wins unless there is a clear reason to defer it. Note any non-obvious ordering decisions with a brief reason in the plan.

**Identify phases.** Group stories into phases where each phase represents a cohesive, deployable slice of the system. A phase is complete when a human can open the app, perform a meaningful action, and observe a working result. Phases are logical delivery milestones, not sprints — they may not be equal in size.

**Enforce vertical slices.** Each story must include everything needed to deliver a working, testable piece of functionality — frontend, backend, database, and any other layers involved. A story that only adds a database table or only adds a UI component is not a vertical slice. The only exception is foundational technical work (database migrations, CI setup, third-party provisioning) that has no user-facing outcome but must exist before feature stories can begin — label these `[Technical]`.

**Surface all dependencies explicitly.** Every story that is blocked by another must name it. If you cannot determine what should be picked up first, the plan is incomplete — resolve it before continuing.

**Preserve open questions.** Consolidate any new questions raised by the subagents that would change the sequencing. Add them to the Open Questions section.

---

## Step 4: Present the plan for review

Before saving the final document, present a summary to the user:

- The number of phases and what each one delivers
- The total story count and breakdown by type (Technical vs Story)
- The recommended order to start work

Ask: "Does this sequencing look right? If any dependencies are wrong, or phases should be merged or split, tell me before I save the plan."

Wait for confirmation before proceeding.

---

## Step 5: Save and present

Derive the feature slug from the feature name — lowercase, kebab-case (e.g. `user-authentication`, `billing-portal`).

If the user has a project folder connected, save the Delivery Plan to:

```
docs/features/<feature-slug>/delivery-plan.md
```

If no project folder is connected, save as `[Feature Name] Delivery Plan.md` in the working directory.

Clean up the intermediate files (`_backend-plan.md`, `_frontend-plan.md`, `_qa-plan.md`, `_devops-plan.md`) from the working directory once the final plan is saved.

Tell the user:

> "The Delivery Plan is ready to pass to the `work-breakdown` skill, which will create the work item tickets in your project management tool. Stories marked [Technical] should be picked up first — they unblock the feature stories. If there are Open Questions in the plan, resolve them before continuing — they may affect phase sequencing."
