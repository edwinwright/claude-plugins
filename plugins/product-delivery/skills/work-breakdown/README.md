# work-breakdown

Turns specified work into a `PLAN.md`: numbered, dependency-ordered vertical slices that a coding agent executes one at a time.

## When to use

Once the work is specified enough to decompose. On the Direct route that is straight after `request-triage`; on Standard, after `backlog-refinement`; on Full, after `delivery-planning`.

Do not run it while the input document has unresolved Open Questions. They produce wrongly scoped slices.

## Inputs

| Input | Required | Notes |
|---|---|---|
| Work order | Yes | `docs/work/<ID>-<slug>/work-order.md`, for the ID, route, and request |
| Requirements document | Standard, Full | From `backlog-refinement` |
| Delivery Plan + Technical Specification | Full | From `delivery-planning` and `technical-design`; the spec supplies acceptance criteria, files, and check commands |

## Output

One file in the work order's folder:

```
docs/work/<ID>-<slug>/PLAN.md
```

- **How to run this**: the rules an executing agent follows. One slice at a time, run the slice's Check before stopping, stop and report after two failed Checks, never improvise a substitute or skip ahead. `review: stop` slices wait for the author to commit; `review: continue` slices commit and carry on; a cloud or background agent treats every slice as `continue` and ends with one pull request.
- **Context, Goal, Done when**: enough *why* to survive the requirements document being archived.
- **Slices**: each with its goal, review mode, files to create and modify, what not to touch, tasks, acceptance criteria with `Verify:` lines, and a Check block of exact commands.

Also adds a one-line pointer to the plan in the repo-root `AGENTS.md`, creating it (and a `CLAUDE.md` symlink) if needed.

## How it works

1. Reads the work order and the documents its route produced, and checks for unresolved Open Questions
2. Proposes the slice list, with a review mode per slice, for confirmation before writing anything
3. Recommends splitting the work order if it runs past about eight slices or splits into independent groups
4. Writes `PLAN.md` from `templates/plan-template.md`
5. Adds the `AGENTS.md` pointer

## Why a plan rather than tickets

Earlier versions wrote an Epic file and one Story file per story. In practice an agent works better from one document it can read top to bottom, with its execution rules at the top and a check at the end of every step, than from a folder of tickets it has to assemble. The slice keeps what a good story carried (files, the *why*, criteria that can be verified, the commands that prove it) and drops the scaffolding.

Tracker issues are still available: `ticket-publish` mirrors each slice into one issue and writes the IDs back into the plan.

## Usage guidelines

This skill runs entirely within the main agent; no subagents are spawned.

| Setting | Recommendation |
|---|---|
| Model | **claude-sonnet-4-6** is sufficient. The task is structured and well constrained by the inputs and the template. |
| Effort | **Low to medium.** |

Plan quality depends on the tech spec on the Full route. Without one, slices carry thinner acceptance criteria and the Check commands come from `AGENTS.md`.

This is the safe step. Edit the plan freely before anything runs; editing a file is free, deleting thirty tracker issues is not.

## Routes

Runs on **every** route, the one step no route skips. What it reads differs by route: the delivery plan on Full, the requirements document on Standard, the work order alone on Direct.

Routes, escalation gates, and the work order format are defined in [`request-triage/references/routes.md`](../request-triage/references/routes.md).

**Next step:** Build from the plan. Run `ticket-publish` first if the slices should exist as tracker issues.

## Files

| File | Purpose |
|---|---|
| `SKILL.md` | Skill instructions |
| `README.md` | This file |
| `templates/plan-template.md` | Template for `PLAN.md` |
| `agents-template.md` | Template for the repo-root `AGENTS.md` agent router |
