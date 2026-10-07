---
name: ticket-publish
description: 'Publish a work order''s PLAN.md to Linear or GitHub Issues: the work order becomes a Linear project or parent issue (a GitHub milestone), each slice an issue with its goal, acceptance criteria, and a link back to the plan, and the created IDs are written back into PLAN.md. A Direct-route work order, such as a bug fix, publishes as a single issue. Use after work-breakdown, once the plan is reviewed. Triggers: "publish the tickets", "push these to Linear", "create GitHub issues from the plan", "upload the tickets". Not for publishing artifacts, notes, packages, or posts.'
---

# ticket-publish

You are mirroring a work order's `PLAN.md` into the user's tracker, one issue per slice.

**Markdown in the repo is the source of truth for content; the tracker owns IDs and status.** The issues carry enough to be worked from a board, and link back to `PLAN.md` for the rest. Nothing is written in the tracker that the plan does not already say.

## Routes

Optional on every route, and not a route step. Run it when the slices should exist in Linear or GitHub; skip it when the plan is enough. It ticks a Steps line only if the user has added one for it, and never adds one. Routes are defined in `../request-triage/references/routes.md`.

**Before you start,** you need a work order and its plan. If there is no work order, say so and offer to run `request-triage` first. If there is a work order but no `PLAN.md`, offer to run `work-breakdown`. This skill creates nothing locally except the IDs it writes back.

---

## Step 1: Read the work order and the plan

Read both:

```
docs/work/<ID>-<slug>/work-order.md
docs/work/<ID>-<slug>/PLAN.md
```

Placement is covered in `../request-triage/references/artefacts.md`; check the host project's `AGENTS.md` or `CLAUDE.md` first. Work started under version 3 of this plugin has a `tickets/` folder and no `PLAN.md`; this skill does not publish that shape. Say so, and offer to run `work-breakdown` to produce a plan from the same inputs.

From the work order take the ID, title, and route. From the plan take, for each slice: its number, goal, why-line, acceptance criteria with their `Verify:` lines, what it depends on, and its `Issue:` line.

**Slices whose `Issue:` line already names an issue are published.** Skip them. This is what makes a re-run after a partial failure safe: it picks up where the last run stopped instead of creating duplicates.

---

## Step 2: Detect the tracker

Check which tracker MCP is connected:

- **Linear**, if the Linear MCP is available
- **GitHub Issues**, if the GitHub MCP is available

If both are connected, ask which to use. If neither is, tell the user and stop.

---

## Step 3: Confirm the destination and the shape

**Linear.** Ask for the team, then how to group the slices:

> - **Project** (default): a Linear project named `[ID]: [Title]`, with one issue per slice in it.
> - **Parent issue**: one issue for the work order, with each slice as a sub-issue.

If the work order's ID is already a Linear issue (it arrived as one, or `request-triage` created it), that issue *is* the work order. Use it as the parent and do not create another; offer to add it and its sub-issues to a project as well, for large work.

Linear has no Epic issue type. Do not look for one.

**GitHub Issues.** Ask for the repository (`owner/repo`). The work order becomes a milestone named `[ID]: [Title]`, and each slice an issue in it.

**Direct route.** One issue for the whole work order, with no project, parent, or milestone. If the work order's ID is already a tracker issue, update that issue instead of creating one: add the plan link and the acceptance criteria to it.

---

## Step 4: Build the plan link

Each issue links to `PLAN.md` on the default branch, so the link still works after the work branch is merged:

```
https://github.com/<owner>/<repo>/blob/<default-branch>/docs/work/<ID>-<slug>/PLAN.md
```

Read `<owner>/<repo>` from `git remote get-url origin`, and the default branch from `git symbolic-ref refs/remotes/origin/HEAD`. If either cannot be read, or the remote is not on GitHub, ask the user for the base URL.

The link resolves only once `PLAN.md` is committed and pushed to the default branch. If it is not there yet, say so: the issues can be created now, but their links will 404 until it is.

---

## Step 5: Present the publish plan

Show what will be created:

- The tracker, team or repository, and grouping (project, parent issue, or milestone)
- Each slice as `Slice N: [goal]`, with what it depends on
- Slices that will be skipped because they already have an issue
- The total count

Ask: "Ready to create these [N] issues in [tool]? Deleting them afterwards is manual, so check the plan first."

Wait for explicit confirmation before creating anything.

---

## Step 6: Create the grouping

Create the Linear project, the parent issue, or the GitHub milestone. Skip this on the Direct route, and skip it if the grouping already exists (the plan's `tracker:` field names it). Save its ID or URL.

---

## Step 7: Create one issue per slice

In slice order. For each:

- **Title:** `Slice N: [goal]`, prefixed with the work order ID where the tracker does not show the grouping in the title (`[ID] slice N: [goal]`).
- **Body:**
  - The slice's goal and its why-line
  - Its acceptance criteria, each with its `Verify:` line, as a checklist
  - `Plan: [link from Step 4]`, with "Slice N" named so the reader knows where to look
- **Grouping:** in the project, as a sub-issue of the parent, or in the milestone.

Do not copy Files, Tasks, or the Check block into the issue. They live in the plan, which is what an agent works from; a second copy in the tracker goes stale the first time the plan is edited.

After each issue is created, **write its ID back into `PLAN.md` immediately**, on that slice's `Issue:` line (`**Issue:** MD-124`, or the URL for GitHub). Writing as you go, rather than at the end, is what lets a failed run resume.

---

## Step 8: Link dependencies

For each slice that depends on another, create a "blocked by" relation between their issues. A slice with no stated dependency depends on the slice before it.

- **Linear:** issue relations (blocks / blocked by).
- **GitHub Issues:** the native blocking relation if the connected tools support it; otherwise add `Blocked by #N` to the issue body.

---

## Step 9: Record and summarise

Set the plan's frontmatter `tracker:` to the grouping: the project URL, parent issue ID, or milestone URL. On the Direct route, the single issue's ID.

If the user added a `ticket-publish` line to the work order's Steps, tick it.

Offer to commit the `PLAN.md` change. Do not push.

Present:

- A link to the project, parent issue, or milestone
- Each created issue, in slice order
- Any slice skipped because it was already published

Tell the user:

> "The slices are in [tool], with their IDs written back into `PLAN.md`. Status lives in the tracker from here; content stays in the plan. If the plan changes, edit `PLAN.md` first and update the affected issue by hand, or re-run this skill for any new slice."

---

## Field mapping

| Plan | Linear | GitHub Issues |
|---|---|---|
| Work order | Project (default) or parent issue | Milestone |
| Slice | Issue (in the project, or a sub-issue) | Issue in the milestone |
| Slice goal + why | Issue title and opening lines | Issue title and opening lines |
| Acceptance criteria + `Verify:` | Checklist in the body | Checklist in the body |
| Depends on | Blocked-by relation | Blocking relation, or `Blocked by #N` |
| Direct-route work order | One issue | One issue |
| Created IDs | Written back to each slice's `Issue:` line and the plan's `tracker:` | Same |
