# acceptance-review

Closes out a delivered work order: verifies it against the criteria that were agreed, promotes what turned out to be durable into the permanent documentation, and archives the spent documents.

## Why it exists

The pipeline used to end at publishing tickets. Nothing checked the built thing against what was specified, and nothing moved what was learned during the build into the documents that outlive it.

That second gap is the expensive one. A requirements document describes a plan; once the work ships, the plan is history. But building it produced things that are now permanently true about the system — a business rule, an invariant, a term with a specific meaning, a decision that will constrain the next five features. If nobody moves those into the glossary, the domain model, and the decision records, they die with the document, and the durable set goes stale one feature at a time.

This skill is the mechanism that stops that.

## When to use

When a work order is built and before it is considered done. **Once per work order,** after the last story — not per story, which fragments the harvest and makes the archive step incoherent.

## Routes

Runs at the close of the **Standard** and **Full** routes. Direct skips it: there is no requirements document to verify against and nothing durable to harvest — the tests and the code review are the acceptance.

Routes, escalation gates, and the work order format are defined in [`request-triage/references/routes.md`](../request-triage/references/routes.md).

## Inputs

| Input | Required | Notes |
|---|---|---|
| The work order folder | Yes | `docs/work/YYYY-MM-<slug>/` — work order, requirements, tech spec, tickets |
| The repository | Yes | The Verification Lens reads it and runs the verification commands |
| The diff or commit range | Helpful | `git log` since the work order was opened is a reasonable default |

## Output

- An acceptance report at `docs/work/_archive/YYYY-MM-<slug>/acceptance-report.md`
- Additions to `docs/product/glossary.md`, `domain-model.md`, and `nfr.md`
- New records under `docs/decisions/`
- The work order's `AGENTS.md` entry removed
- The work folder moved to `docs/work/_archive/`, every file stamped `status: superseded`

## How it works

```
Verification Lens  +  Harvest Lens        ← run in parallel
        ↓                    ↓
  PASS / FAIL /        glossary, domain,
  UNVERIFIABLE         nfr, decision
  per criterion        candidates
        ↓                    ↓
   Main agent → GATE → confirm → promote → archive
```

1. **Verification Lens** — runs the commands, finds the tests, reads the code. Returns a verdict per criterion with evidence.
2. **Harvest Lens** — reads the diff against the requirements document and finds what got *learned* rather than what got planned. Pre-applies the three significance tests to every decision candidate.
3. **Main agent** — gates on the verification result, confirms the harvest with you, promotes each item using the skill that owns its format, then archives.

### The gate

**Any `FAIL` or `UNVERIFIABLE` stops the run.** Nothing is harvested, nothing is archived, nothing is amended.

`UNVERIFIABLE` blocks as firmly as `FAIL`, which surprises people. It means nobody can tell whether the thing works — a worse position than knowing it does not. It usually means a criterion was written without a check, and the fix is to add one and re-run.

You can override the gate. If you do, the waiver is recorded in the report and carried into the archived work order, with which criteria were waived and on whose say-so. It is never silent.

### It never deletes

The work folder is moved to `docs/work/_archive/`, not removed. Every file is stamped:

```yaml
status: superseded
archived: YYYY-MM-DD
harvested_to: [docs/product/glossary.md, docs/decisions/architecture/0007-webhook-retries.md]
```

The stamp is what stops an agent finding a superseded specification and treating it as current — the one real cost of archiving rather than deleting, and worth paying to get content back without git archaeology. The skill prints the `rm -rf` for you to run once you are satisfied nothing was lost.

## Usage guidelines

| Agent | Model | Effort | Reason |
|---|---|---|---|
| Verification Lens | claude-sonnet-4-6 | High | Its output gates everything downstream; a false pass closes a work order that is not done |
| Harvest Lens | claude-sonnet-4-6 | Medium | Pattern-matching against known destinations |

**Formats are not restated here.** Confirmed glossary and domain-model additions are handed to `domain-modelling` in append mode; each confirmed decision is handed to `decision-record`. Those skills own their shapes. The only file this skill writes directly is `nfr.md`, where the change is a numbered row rather than a shape.

**It is sceptical on your behalf.** A term earns a glossary entry only if it means something specific to this codebase. An invariant qualifies only if violating it would be a bug anywhere in the system. A decision earns a record only if it clears all three significance tests. A glossary quietly filling with terms nobody uses damages trust as much as a stale one.

## Files

| File | Purpose |
|---|---|
| `SKILL.md` | Orchestration instructions |
| `README.md` | This file |
| `acceptance-report-template.md` | Output template for the acceptance report |
| `archive-readme-template.md` | Written once into `docs/work/_archive/README.md` |
| `prompts/verification-lens.md` | Verification Lens subagent instructions |
| `prompts/harvest-lens.md` | Harvest Lens subagent instructions |
