# ticket-publish

Mirrors a work order's `PLAN.md` into Linear or GitHub Issues, one issue per slice, and writes the issue IDs back into the plan.

## When to use

After `work-breakdown` has written `PLAN.md` and you have reviewed it, when the slices should exist on a board. Skip it when the plan is enough.

Issues created in a tracker cannot be bulk-deleted easily. Review the plan first.

## Requirements

A connected tracker MCP:

- **Linear**, via the Linear MCP
- **GitHub Issues**, via the GitHub MCP

## Inputs

| Input | Required | Notes |
|---|---|---|
| `work-order.md` | Yes | For the ID, title, and route |
| `PLAN.md` | Yes | Written by `work-breakdown`; the slices are what gets published |
| Team or repository, and grouping | Yes | Asked at runtime |

## Output

| Plan | Linear | GitHub Issues |
|---|---|---|
| Work order | A project (default) or a parent issue | A milestone |
| Each slice | An issue with its goal, acceptance criteria, and a link to `PLAN.md` on the default branch | The same, in the milestone |
| Dependencies | Blocked-by relations | Blocking relations, or `Blocked by #N` |
| Direct-route work order | One issue | One issue |

The created IDs are written back into `PLAN.md`, beside each slice and in the plan's `tracker:` field. The project or milestone description links back to the work order folder.

## How it works

1. Reads the work order and the plan; skips slices that already carry an issue ID
2. Detects the connected tracker
3. Asks for the team or repository, and on Linear whether to group by project or parent issue
4. Presents everything it will create, and waits for confirmation
5. Creates the grouping, then one issue per slice, writing each ID back into the plan as it goes
6. Links dependencies
7. Offers to commit the plan change; never pushes

## Source of truth

The repo is the source of truth for content and work order IDs; the tracker owns the IDs and status of published issues. Issues carry the goal and the acceptance criteria so they can be worked from a board, and link to the plan for the files, tasks, and check commands. Those are not copied: a second copy goes stale the first time the plan is edited.

## Usage guidelines

This skill runs entirely within the main agent; no subagents are spawned. The work is mostly calls to the tracker.

| Setting | Recommendation |
|---|---|
| Model | **claude-sonnet-4-6** is sufficient. |
| Effort | **Low.** The content is already in the plan; this skill maps and uploads it. |

Re-running is safe. IDs are written back after each issue is created, and any slice that already has one is skipped, so a run that fails partway resumes rather than duplicating.

## Routes

Optional on every route, and not a route step. It appears in a work order's Steps only if you add it.

Routes, escalation gates, and the work order format are defined in [`request-triage/references/routes.md`](../request-triage/references/routes.md).

**Next step:** Build from the plan. Status now lives in the tracker.

## Files

| File | Purpose |
|---|---|
| `SKILL.md` | Skill instructions |
| `README.md` | This file |
