# domain-modelling

Bootstraps and maintains the project's shared vocabulary (`glossary.md`) and domain model (`domain-model.md`).

## When to use

- **New product** — run after `product-definition` to document the domain before feature work begins
- **New feature** — run when a feature introduces new entities or terms that should be formalised
- **Existing codebase** — run cold to document a legacy domain before adding new features

## Inputs

| Input | Required | Notes |
|---|---|---|
| Description of the domain / product | Yes (bootstrap) | Can be brief or detailed |
| Existing PRD, tech spec, or README | No | Scanned for candidate terms |
| New PRD or feature description | Yes (append) | Used to identify gaps in existing docs |

## Output

Two files written or updated at:

```
docs/product/
  glossary.md        ← ubiquitous language: Term | Definition | Aliases | Notes
  domain-model.md    ← entities, relationships, invariants, mermaid diagram
```

## How it works

**Bootstrap mode** (when neither file exists):
1. Asks up to 7 questions to elicit core entities, verbs, and invariants
2. Optionally scans provided docs for candidate terms
3. Writes both files from templates

**Append mode** (when files already exist):
1. Reads the existing files
2. Reads the new input (PRD, feature description, etc.) and identifies gaps
3. Proposes additions for confirmation
4. Edits both files in place

## Usage guidelines

| Setting | Recommendation |
|---|---|
| Model | **claude-sonnet-4-6** is sufficient for most codebases. Use **claude-opus-4-7** for complex domains with many interrelated entities. |
| Effort | **Medium** for bootstrap. **Low** for append (targeted gap-fill). |

The quality of the output depends heavily on what you provide upfront. Attach your README, API docs, or existing codebase context for the richest result.

## Routes

Standalone, on any route, at any point. `acceptance-review` also calls it in append mode to promote terms and invariants discovered during a build.

Routes, escalation gates, and the work order format are defined in [`request-triage/references/routes.md`](../request-triage/references/routes.md).

`domain-modelling` is standalone — it is not a pipeline step, but it feeds the pipeline. `technical-design` and `backlog-refinement` read the existing glossary and domain model as context when processing new features.

## Files

| File | Purpose |
|---|---|
| `SKILL.md` | Skill instructions |
| `README.md` | This file |
| `glossary-template.md` | Template for the glossary output |
| `domain-model-template.md` | Template for the domain model output |
