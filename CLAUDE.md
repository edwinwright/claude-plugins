# claude-plugins — Claude Context

A Claude plugin marketplace holding two plugins: `delivery-design` (an end-to-end SDLC pipeline) and `knowledge` (note and document authoring for an Obsidian vault). Public, and intended to double as a portfolio artifact demonstrating AI-engineering practice. This repo is the single source of truth for these plugins; installed copies elsewhere should be treated as stale.

> [!warning] Public repo — no personal data
> Never commit PII, client names, credentials, private vault structure, or commercial terms. Personal-*flavoured* examples are fine and often useful — a runbook for renewing a passport shows the skills generalise past SDLC. Personal *data* is not. Review diffs for leakage before every push.

---

## Repo Shape

```
.claude-plugin/marketplace.json      catalog — lists every plugin (fixed path)
plugins/
├── delivery-design/                 end-to-end SDLC delivery pipeline
│   ├── .claude-plugin/plugin.json
│   └── skills/{product-brief, domain-doc, feature-request, feature-brief,
│               feature-design, feature-plan, feature-tickets,
│               feature-publish, decision-record}/
└── knowledge/                       document shape + note-writing workflows
    ├── .claude-plugin/plugin.json
    └── skills/{note-formats, obsidian-note-writer, session-synthesis, ops-docs}/
```

The `delivery-design` skills form a pipeline: `product-brief → domain-doc → feature-request → feature-brief → feature-design → feature-plan → feature-tickets → feature-publish`, with `decision-record` available throughout.

In `knowledge`, `note-formats` is the source of truth for shape and style; the other three own workflows and call it.

The two plugins are independent — no file in one references a file in the other. Keep it that way: there is no mechanism to declare a cross-plugin dependency, so a skill that reaches into a sibling plugin fails silently when that plugin is not installed. A `documentation` plugin previously shipped with exactly this defect (its only skill delegated all three of its formats to `note-formats` in another plugin) and was merged into `knowledge` to fix it.

---

## How Plugins Work (facts, not assumptions)

- **One marketplace per repo.** `marketplace.json` lives at the fixed path `.claude-plugin/marketplace.json`. There is no mechanism for multiple marketplaces in one repo.
- **Every plugin needs `.claude-plugin/plugin.json`** with a required `version` field (used to detect updates). Missing plugin.json or missing marketplace entry = won't install.
- **Skills are auto-discovered** by walking `skills/*/SKILL.md`. They are **not** declared in `plugin.json`. Same for `commands/`.
- **`category`** is a freeform string; `productivity` is used here.
- **Marketplace `name` must not contain `claude`** — Cowork rejects it as impersonating an official marketplace. See `KNOWN-ISSUES.md`.
- Plugins are not surface-specific — the same plugin works in Claude Code and Cowork. Hooks and sub-agents only run in Cowork.
- MCP servers can be bundled in a plugin, but verify the exact mechanism (`plugin.json` key vs. `.mcp.json`) against current docs before adding one.

## Keep descriptions honest and in sync

The plugin description in `marketplace.json` and in the plugin's own `plugin.json` must match and must reflect what the skills actually do. When you add or remove a skill, update both descriptions and bump `version`.

---

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
- After changing a plugin's contents, bump its `version` so installs detect the update.
- Prefer widening a `description:` over adding a skill. See the `zettelkasten` merge above.
