# claude-plugins

A Claude plugin marketplace: turning an idea into shipped, tracked work, writing the code itself to a settled standard, and turning what you learn along the way into notes you can find again. All ship as composable, natural-language-triggered skills that run in Claude Code and Cowork.

Three plugins, seventeen skills.

| Plugin | Skills | For |
|---|---|---|
| [`delivery-design`](#delivery-design) | 11 | An end-to-end SDLC pipeline: request → requirements → spec → tickets → build → verified acceptance, on one of three routes |
| [`engineering`](#engineering) | 1 | Personal coding conventions: the judgement calls a linter can't make, plus the standard setup a new project gets |
| [`knowledge`](#knowledge) | 5 | Learning and knowledge capture for an Obsidian vault: what shape a document should take, the workflows that produce it, and coached sessions that build understanding while you work |

They install independently and share no files. Install any combination.

## Design principles

All three plugins are built around a small set of deliberate constraints rather than an accumulation of features:

- **One source of truth: local markdown.** Project-management tools (Linear, GitHub Issues) are sync targets, never the origin. State lives where the code lives.
- **A document is worth writing only if its "why" can't be recovered from the code.** That single test gates every decision record — it keeps documentation from drifting into noise.
- **Durable and transient documents are kept apart.** A glossary describes how things *are*; a requirements document describes a change we *propose to make*. Filed together, the second quietly rots the first.
- **The heaviest process must never be the default.** Small work takes a short route, and escalating to the long one requires naming a reason that is actually true.
- **Don't reinvent formats that already have standards.** Decisions use MADR-style records; agent context uses `AGENTS.md`; nothing bespoke where a convention already exists.
- **Formats are data; skills are workflows.** A shape is defined once, in the skill that owns it. Every other skill owns a *process* and delegates shape. If a convention appears in two skills, that is a bug.
- **Skills never hardcode where a file goes.** Placement, naming, and folder structure are read from the host project or vault at runtime, so the same skill works in any of them.

## delivery-design

Everything enters at `request-triage`, which decides whether the work should happen and which of three routes it takes.

```
A — Direct     request-triage → work-breakdown → build
                 a bug fix, a copy change — requirements would only restate the request

B — Standard   request-triage → backlog-refinement → work-breakdown → build → acceptance-review
                 the default: a feature, days of work, a familiar domain

C — Full       requirements-discovery → product-definition → domain-modelling → request-triage →
               backlog-refinement → technical-design → delivery-planning →
               work-breakdown → build → acceptance-review
                 a new product, or work that trips an escalation gate

decision-record is available throughout, at any point a costly, hard-to-reverse decision is made.
```

Route B is the documented default. Escalating to C requires **naming a gate** that is actually true — new domain concepts, crossing bounded contexts, a numbered non-functional requirement, a third-party integration outside our control, or a decision that is costly to reverse. Without that rule the full ceremony becomes the path of least resistance on small work, which is how most process dies.

| Skill | Routes | Does |
|---|---|---|
| `requirements-discovery` | C | Elicitation, stakeholder mapping, current state, problem framing |
| `product-definition` | C | Idea → vision, MoSCoW backlog, non-functional baseline |
| `domain-modelling` | any | Bootstraps and maintains the glossary + domain model |
| `request-triage` | all | The front door — build or park, and which route |
| `backlog-refinement` | B, C | Backlog item → requirements document |
| `technical-design` | C | Requirements → Technical Specification (virtual dev team of four lenses) |
| `delivery-planning` | C | Requirements + spec → sequenced Delivery Plan |
| `work-breakdown` | all | Specified work → local Epic + Story ticket files |
| `ticket-publish` | optional | Local ticket files → Linear or GitHub Issues |
| `acceptance-review` | B, C | Verify against criteria, harvest what is durable, archive what is spent |
| `decision-record` | any | MADR-style record with a significance gate that refuses trivial ones |

`acceptance-review` is what keeps the rest honest. When work is built it verifies the result against the criteria that were agreed, then promotes what turned out to be durable — a business rule, an invariant, a term, a contested decision — into the glossary, domain model, and decision records, and archives the spent documents so they stop being read as current. Without it, everything learned during a build stays locked in a document describing a plan that no longer exists.

## engineering

`coding-conventions` holds only the judgement calls a linter cannot make — naming that carries meaning, function and module boundaries, error handling, server/client component choice, feature-based folder structure. Anything ESLint decides deterministically stays in the lint config instead.

| Skill | Does |
|---|---|
| `coding-conventions` | TypeScript, React, and Next.js conventions, plus the standard setup a new repository gets |

## knowledge

`note-formats` is the source of truth for document shape and house style. The other skills own workflows and call it rather than restating it.

| Skill | Does |
|---|---|
| `note-formats` | Decides which of eleven shapes a document should take, and defines each one |
| `obsidian-note-writer` | Create, enhance, and refactor notes — search before creating, safe splits and merges |
| `session-synthesis` | Conversation or transcript → conceptual map → notes |
| `ops-docs` | A messy process → Checklist, Runbook, or Playbook |
| `guided-practice` | Turns a real task into a coached, step-by-step learning session — a real deliverable still gets built, just collaboratively; hands off to `session-synthesis` at the close to capture what was learned |

The eleven shapes split into **knowledge** shapes, read to build understanding (Atomic Note, Overview, Hub, Decision Guide, Guide, Map of Content), and **operational** shapes, read to get something done (Checklist, Runbook, Playbook, Standard Note, Decision Record). Choosing between them is the hard part and the reason the skill exists — `references/choosing-a-format.md` carries the criteria, the pairs that get confused, and worked examples.

## Repository structure

```
.claude-plugin/marketplace.json      marketplace catalog (fixed path)
docs/                                plugin-mechanics and skill-authoring doctrine, read on demand
plugins/
├── delivery-design/
│   ├── .claude-plugin/plugin.json   plugin manifest (versioned)
│   └── skills/                      one directory per skill: SKILL.md + templates + subagent prompts
│       └── request-triage/
│           └── references/          routes and artefact-lifespan doctrine, referenced by every other skill
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

All seventeen skills ship and are in active use. `CLAUDE.md` carries the conventions these plugins are developed under, including why two overlapping skills were merged into one.

`delivery-design` 2.0 renamed its skills after the processes they run rather than the artefacts they emit, replaced the single pipeline with three routes, and added `requirements-discovery` and `acceptance-review` at either end. The old names (`feature-brief`, `feature-design`, `feature-tickets`, and the rest) are gone — there is no alias mechanism in the plugin system, so this is a hard rename.

## Licence

MIT — see [LICENSE](LICENSE).
