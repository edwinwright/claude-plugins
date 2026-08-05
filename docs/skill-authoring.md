# Skill authoring doctrine

Relevant when writing a new skill, or restructuring, merging, or splitting an existing one.

## Formats are data; skills are workflows

Everything about what a document *looks like* — the taxonomy, the decision flow between shapes, the templates, the notation — lives in one place and nowhere else. Every other skill owns a *process* and delegates shape to it.

- **If a convention appears in two skills, that is a bug.** One skill owns it; the other points at it.
- Cross-skill dependencies must be stated in `SKILL.md`, not only in `README.md` — the model never loads the README, so a dependency recorded only there will get copied instead of followed.
- Shared doctrine lives as a `references/` file inside the owning skill; templates live in `assets/` or `templates/`. Keep `SKILL.md` thin and let the agent read only what the task needs.

**Where the boundary sits.** `note-formats` owns shapes where *choosing between shapes* is a real question — is this a runbook or a playbook? A template with exactly one producer and one consumer (a PRD written by `feature-brief`, read by `feature-design`) stays with its skill. There is no classification problem to solve there, and centralising it would add an indirection hop and a cross-plugin dependency for no benefit.

**Why this convention exists.** An earlier rule said skills with independent triggers should stay separate. That produced `zettelkasten` and `obsidian-note-writer` sharing ~45% of their content verbatim, because both needed the same structural doctrine and neither owned it. They were merged into `note-formats`. Trigger breadth is a `description:` problem, not a skill-count problem — write the description to cover both trigger families instead of splitting the skill.

## Skill vs. host-project doctrine

Portable methodology lives in the skill; project- or vault-specific structure is read at runtime from the host's own `CLAUDE.md`.

| Lives in the host's `CLAUDE.md` | Lives in the skill |
|---|---|
| Folder structure, where each document type goes | How to recognise which type something is |
| Naming conventions, callout formats, MOC types | General writing/structuring principles per type |
| "Where does this belong" decision tree | The dedup-before-creating and safe-refactor procedures |
| Anything that may differ between projects | Portable methodology usable anywhere |

Skills that create or move files must **read the host's `CLAUDE.md`** for structure, naming, and placement before acting — make that dependency explicit in `SKILL.md` rather than baking a folder tree into the skill. Obsidian *syntax* is fine; a specific *folder tree* is not.

## Conventions for developing skills here

- Direct and concise skill instructions; British English; no personal or client-identifying content.
- After changing a plugin's contents — including adding, renaming, or removing a skill — bump its `version` so installs detect the update.
- Prefer widening a `description:` over adding a skill. See the `zettelkasten` merge above.
