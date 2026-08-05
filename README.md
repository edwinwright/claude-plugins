# claude-plugins

A Claude plugin marketplace: turning an idea into shipped, tracked work, writing the code itself to a settled standard, and turning what you learn along the way into notes you can find again. All ship as composable, natural-language-triggered skills that run in Claude Code and Cowork.

Three plugins, fourteen skills.

| Plugin | Skills | For |
|---|---|---|
| [`delivery-design`](#delivery-design) | 9 | An end-to-end SDLC pipeline: idea → PRD → tech spec → delivery plan → tickets → Linear/GitHub |
| [`engineering`](#engineering) | 1 | Personal coding conventions: the judgement calls a linter can't make, plus the standard setup a new project gets |
| [`knowledge`](#knowledge) | 4 | Note and document authoring for an Obsidian vault: what shape a document should take, and the workflows that produce it |

They install independently and share no files. Install any combination.

## Design principles

All three plugins are built around a small set of deliberate constraints rather than an accumulation of features:

- **One source of truth: local markdown.** Project-management tools (Linear, GitHub Issues) are sync targets, never the origin. State lives where the code lives.
- **A document is worth writing only if its "why" can't be recovered from the code.** That single test gates every decision record — it keeps documentation from drifting into noise.
- **Don't reinvent formats that already have standards.** Decisions use MADR-style records; agent context uses `AGENTS.md`; nothing bespoke where a convention already exists.
- **Formats are data; skills are workflows.** A shape is defined once, in the skill that owns it. Every other skill owns a *process* and delegates shape. If a convention appears in two skills, that is a bug.
- **Skills never hardcode where a file goes.** Placement, naming, and folder structure are read from the host project or vault at runtime, so the same skill works in any of them.

## delivery-design

```
PRODUCT workflow  (new product)
  product-brief → domain-doc → product-backlog

FEATURE workflow  (per backlog item)
  feature-request → feature-brief → feature-design → feature-plan → feature-tickets → feature-publish
      intake          idea→PRD        PRD→spec         spec→plan       plan→files       files→Linear/GitHub

decision-record is available throughout, at any point a costly, hard-to-reverse decision is made.
```

A brand-new product runs the product workflow first, then pushes each backlog item through the feature workflow. A new feature on an existing product skips straight to `feature-brief`.

| Skill | Does |
|---|---|
| `product-brief` | Idea → Product Brief + MoSCoW-prioritised backlog |
| `domain-doc` | Bootstraps and maintains the glossary + domain model |
| `feature-request` | Intake triage — the front door before a PRD |
| `feature-brief` | Feature idea → PRD |
| `feature-design` | PRD → Technical Specification (runs a virtual dev team of specialist lenses) |
| `feature-plan` | PRD + Tech Spec → sequenced Delivery Plan |
| `feature-tickets` | Delivery Plan → local Epic + Story ticket files |
| `feature-publish` | Local ticket files → Linear or GitHub Issues |
| `decision-record` | MADR-style decision record with a significance gate that refuses trivial ones |

## engineering

`coding-conventions` holds only the judgement calls a linter cannot make — naming that carries meaning, function and module boundaries, error handling, server/client component choice, feature-based folder structure. Anything ESLint decides deterministically stays in the lint config instead.

| Skill | Does |
|---|---|
| `coding-conventions` | TypeScript, React, and Next.js conventions, plus the standard setup a new repository gets |

## knowledge

`note-formats` is the source of truth for document shape and house style. The other three own workflows and call it rather than restating it.

| Skill | Does |
|---|---|
| `note-formats` | Decides which of eleven shapes a document should take, and defines each one |
| `obsidian-note-writer` | Create, enhance, and refactor notes — search before creating, safe splits and merges |
| `session-synthesis` | Conversation or transcript → conceptual map → notes |
| `ops-docs` | A messy process → Checklist, Runbook, or Playbook |

The eleven shapes split into **knowledge** shapes, read to build understanding (Atomic Note, Overview, Hub, Decision Guide, Guide, Map of Content), and **operational** shapes, read to get something done (Checklist, Runbook, Playbook, Standard Note, Decision Record). Choosing between them is the hard part and the reason the skill exists — `references/choosing-a-format.md` carries the criteria, the pairs that get confused, and worked examples.

## Repository structure

```
.claude-plugin/marketplace.json      marketplace catalog (fixed path)
docs/                                plugin-mechanics and skill-authoring doctrine, read on demand
plugins/
├── delivery-design/
│   ├── .claude-plugin/plugin.json   plugin manifest (versioned)
│   └── skills/                      one directory per skill: SKILL.md + templates + subagent prompts
├── engineering/
│   ├── .claude-plugin/plugin.json
│   └── skills/
│       └── coding-conventions/
│           └── references/          typescript, react-nextjs, project-setup doctrine
└── knowledge/
    ├── .claude-plugin/plugin.json
    └── skills/
        └── note-formats/
            ├── references/          doctrine: choosing a format, structure, Obsidian style
            └── assets/              one skeleton per shape
```

Skills are auto-discovered by walking `skills/*/SKILL.md`; they are not declared in the manifest.

## Install

```
/plugin marketplace add edwinwright/claude-plugins
/plugin install delivery-design
/plugin install engineering
/plugin install knowledge
```

Skills then trigger on natural language — see each skill's `SKILL.md` for its trigger phrases.

`KNOWN-ISSUES.md` documents an upstream Cowork bug that can leave installed plugins showing a stale version of this repo. If a skill behaves as though a recent change never happened, start there.

## Status

All fourteen skills ship and are in active use. `CLAUDE.md` carries the conventions these plugins are developed under, including why two overlapping skills were merged into one.

## Licence

MIT — see [LICENSE](LICENSE).
