# feature-tickets

Breaks a Delivery Plan into an Epic and Stories, saved as local Markdown files ready for review.

## When to use

Use this after `feature-plan` has produced a Delivery Plan. It is step four of the feature pipeline:

```
feature-brief → feature-design → feature-plan → feature-tickets → feature-publish
```

Do not run this skill if the Delivery Plan has unresolved Open Questions — they will cause stories to be scoped or phased incorrectly.

Run `feature-publish` afterwards to push the generated files to Linear or GitHub Issues.

## Inputs

| Input | Required | Notes |
|---|---|---|
| Delivery Plan | Yes | From `feature-plan`, as a file or pasted content |
| Technical Specification | No | Strongly recommended — used to pull acceptance criteria and task breakdowns into story bodies |

## Output

Local Markdown files written to a `tickets/` subfolder:

- `_epic.md` — the Epic ticket
- `[story-name].md` — one file per story, named in kebab-case

Each file includes frontmatter (`ticket_type`, `phase`, `depends_on`, `epic`) that `feature-publish` reads when creating tickets in the project management tool.

## How it works

1. Reads the Delivery Plan and checks for unresolved Open Questions
2. Presents the full story list for review before generating anything
3. Generates `_epic.md` from `templates/epic-template.md`
4. Generates one `.md` file per story from `templates/story-template.md`, in phase order
5. Presents the list of generated files and prompts the user to review before deploying

No external tool connections are required. All output is local.

## Usage guidelines

This skill runs entirely within the main agent — no subagents are spawned.

| Setting | Recommendation |
|---|---|
| Model | **claude-sonnet-4-6** is sufficient. The task is structured and well-constrained by the Delivery Plan and templates. |
| Effort | **Low to medium.** |

Ticket quality depends on the Tech Spec. Passing only the Delivery Plan produces thinner stories. Passing both the Delivery Plan and the Tech Spec produces richer acceptance criteria and task breakdowns.

This is the safe step — review and edit the generated files before running `feature-publish`. File changes are free; deleting 30 tickets from Linear is not.

## Pipeline

```
feature-brief  →  feature-design  →  feature-plan  →  feature-tickets  →  feature-publish
idea → PRD        PRD → Tech Spec   Tech Spec → Plan  Plan → local files  files → tickets
```

**Next step:** Review the files in `tickets/`, then run `feature-publish`.

## Files

| File | Purpose |
|---|---|
| `SKILL.md` | Skill instructions |
| `README.md` | This file |
| `templates/epic-template.md` | Template for the Epic file |
| `templates/story-template.md` | Template for each Story file |
