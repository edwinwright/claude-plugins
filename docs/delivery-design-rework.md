# delivery-design pipeline rework

> [!warning] Transient
> This is the work order for the rework itself, kept only while it is in flight. Phase 9 deletes it. Treat it as the convention it introduces: transient documents describe a proposed change and do not outlive it.

---

## Context

`delivery-design` names its skills after the artefacts they emit (`feature-brief`, `domain-doc`, `feature-tickets`) rather than the processes they run. Three consequences:

1. **Vocabulary drift.** The names don't match how SDLC and business analysis are actually discussed, so the skills are harder to recall and harder to explain.
2. **One path, always the full path.** The pipeline is a fixed six-step chain from triage to publish. A copy change and a payments integration take the same route, so the full ceremony is the path of least resistance on small work.
3. **Nothing closes the loop.** The pipeline ends at `feature-publish`. Nothing verifies the built thing against what was specified, and nothing harvests durable content out of the transient documents. The glossary, domain model, and decision records go stale silently — the failure mode the durable documentation set exists to prevent.

This rework renames nine skills after their processes, adds discovery at the front and acceptance at the back, and replaces the single chain with three routes selected at triage. Acceptance is the highest-value part: it is the mechanism that keeps the durable set current.

**Decisions taken during planning:** two-word names throughout, prioritising trigger reliability over brevity; hard rename with no stubs and no alias triggers; transient documents in `docs/work/YYYY-MM-<slug>/`; acceptance **archives** rather than deletes, leaving the final delete as a manual step.

---

## Target roster (11 skills)

Every name is now a two-word `<qualifier>-<process>` compound. That is deliberate: it makes each name distinctive enough to trigger reliably, and it makes the roster read as one consistent set.

| Skill | From | Routes | Emits |
|---|---|---|---|
| `requirements-discovery` | **new** | C | problem statement, stakeholder map, current state |
| `product-definition` | `product-brief` | C | `vision.md`, `product-backlog.md`, `nfr.md` |
| `domain-modelling` | `domain-doc` | C + standalone | `glossary.md`, `domain-model.md` |
| `request-triage` | `feature-request` | A, B, C — entry point | `work-order.md`, or a backlog row |
| `backlog-refinement` | `feature-brief` | B, C | `requirements.md` |
| `technical-design` | `feature-design` | C | `tech-spec.md` |
| `delivery-planning` | `feature-plan` | C | `delivery-plan.md` |
| `work-breakdown` | `feature-tickets` | A, B, C | `tickets/`, `AGENTS.md` entry |
| `ticket-publish` | `feature-publish` | optional, any route | Linear / GitHub issues |
| `acceptance-review` | **new** | B, C | glossary + domain model + ADR additions; archives the work folder |
| `decision-record` | unchanged | any point | `docs/decisions/<scope>/NNNN-<slug>.md` |

### Why these names rather than the single words

| Proposed | Chosen | Reason |
|---|---|---|
| `triage` | `request-triage` | Standard BA/support term. Covers bugs and change asks, not just features. Kills the "triage my inbox / triage these logs" collision. |
| `refinement` | `backlog-refinement` | *The* standard Agile term, and its input genuinely is a backlog item. Removes the collision with generic "refine this". |
| `discovery` | `requirements-discovery` | Distinguishes it from service discovery and legal discovery, and carries the elicitation sense without dropping the more familiar "discovery". |
| `acceptance` | `acceptance-review` | Bare "acceptance" collides with acceptance *testing*, which is only one of the three things this skill does. |
| `release-planning` | `delivery-planning` | Your call, and the right one — it plans one work order's phases, not a release across features, and it keeps the skill name aligned with `delivery-plan.md`. |
| `publish` | `ticket-publish` | Names the object. Avoids competing with publishing artifacts, notes, packages, posts. |

### Routes

```
A — Direct     request-triage → work-breakdown → build
B — Standard   request-triage → backlog-refinement → work-breakdown → build → acceptance-review
C — Full       requirements-discovery → product-definition → domain-modelling → request-triage →
               backlog-refinement → technical-design → delivery-planning →
               work-breakdown → build → acceptance-review
```

`build` is a plain-text marker, not a skill — no skill is created for it and route diagrams must not imply one. `ticket-publish` is optional on every route and is not a route member. `decision-record` is available at any point.

**Route B is the documented default.** Escalating to C requires naming a gate:

- The work introduces domain concepts not already in `glossary.md`
- It changes behaviour across more than one bounded context
- It carries a real non-functional requirement — a numbered delta against `docs/product/nfr.md`, not a wish
- It involves a third-party integration outside our control
- A decision inside it is costly or slow to reverse

The NFR gate is checkable because Phase 5 gives NFRs a single numbered baseline. A wish has no number and no delta.

---

## Artefact locations and lifespans

```
docs/
├── product/                          DURABLE — present tense, lives with the code
│   ├── vision.md                     product-definition
│   ├── product-backlog.md            product-definition; appended by request-triage
│   ├── glossary.md                   domain-modelling; appended by acceptance-review
│   ├── domain-model.md               domain-modelling; appended by acceptance-review
│   └── nfr.md                        product-definition; amended by acceptance-review
├── decisions/<scope>/NNNN-<slug>.md  DURABLE — decision-record; fed by acceptance-review
└── work/                             TRANSIENT
    ├── 2026-08-csv-export/           ← one folder per work order, dated and named
    │   ├── work-order.md             request-triage — route, gates, triaged summary
    │   ├── requirements.md           backlog-refinement
    │   ├── tech-spec.md              technical-design
    │   ├── delivery-plan.md          delivery-planning
    │   └── tickets/                  work-breakdown
    └── _archive/
        ├── README.md                 "Superseded. Not current. Safe to delete."
        └── 2026-08-stripe-billing/   moved here by acceptance-review
```

**Yes — `requirements.md` lives inside the named work folder** (your question 4). The folder is the work order: `docs/work/YYYY-MM-<slug>/`, where the slug is derived from the request the same way feature slugs are derived today. Everything transient for that piece of work sits together and moves together.

Two changes of principle:

**These paths become defaults, not hardcoded facts.** The repo README claims "Skills never hardcode where a file goes — placement is read from the host project at runtime", but all nine current skills hardcode `docs/product/` and `docs/features/`. Every skill gets a line stating the default and directing it to check the host's `AGENTS.md` or `CLAUDE.md` for an override first. This closes an existing gap between stated doctrine and shipped behaviour.

**`AGENTS.md` maintenance moves from `technical-design` to `work-breakdown`.** It currently lives in `feature-design/SKILL.md:123-143`, so routes A and B would never produce an agent router entry. `work-breakdown` runs on every route and is the last step before build — exactly when the router matters. Entries point at the work folder while work is in flight; `acceptance-review` removes the entry when it archives the folder, so no entry outlives its target.

### Archive, don't delete

`acceptance-review` never deletes. It `git mv`s the work folder to `docs/work/_archive/<slug>/` and stamps every file in it:

```yaml
status: superseded
archived: YYYY-MM-DD
harvested_to: [docs/product/glossary.md, docs/decisions/architecture/0007-webhook-retries.md]
```

Then it prints the delete command for you to run when you are ready:

```
rm -rf docs/work/_archive/2026-08-stripe-billing
```

The `status: superseded` stamp plus `_archive/README.md` are the mitigation for the one real cost of archiving — an agent finding a stale spec and treating it as current. `AGENTS.md` never points into `_archive/`.

---

## Rename mechanics

Skills are discovered by walking `skills/*/SKILL.md`; the directory name and the frontmatter `name:` must agree. Each rename is four mechanical edits plus a semantic one:

1. `git mv plugins/delivery-design/skills/<old> plugins/delivery-design/skills/<new>`
2. `name:` in `SKILL.md` frontmatter
3. The `# <old>` H1 in `SKILL.md` and `README.md`
4. Every cross-reference (below)
5. `description:` — rewritten, not find-and-replaced (Phase 2)

### Cross-reference surface

About 90 references across seven categories. Grep alone will not find them all, because the pipeline is also encoded as prose and as numbered lists.

| Category | Locations |
|---|---|
| Frontmatter descriptions | `feature-design`, `feature-plan`, `feature-tickets`, `feature-publish`, `product-brief` name other skills in `description:`; `feature-request`, `feature-brief`, `domain-doc` name them in prose |
| SKILL.md pipeline rosters | `feature-brief:10-17`, `feature-design:10-15`, `feature-plan:10-15`, `feature-tickets:10-16`, `feature-publish:10-16`, `product-brief:10-15`, `feature-request:12-14` |
| SKILL.md hand-off quotes | Closing "tell the user" block in every pipeline skill |
| **Templates** — leak skill names into generated artefacts | `prd-template.md:70`, `tech-spec-template.md:121`, `delivery-plan-template.md:82,98,106`, `epic-template.md:29`, `story-template.md:73`, `product-backlog-template.md:3` |
| Subagent prompts | `feature-request/prompts/product-lens.md:37` (also references a non-existent `initiative-brief` — delete that) |
| Skill READMEs | All eight non-`decision-record` READMEs carry the pipeline twice: a "When to use" block near line 10 and a "Pipeline" block near the end |
| Outside the plugin | `README.md` ×13 (roster table, both workflow diagrams, skill count "9", the `idea → PRD → tech spec` header line); `docs/skill-authoring.md:13`; `plugins/knowledge/skills/note-formats/SKILL.md:67` |

`decision-record` names no other skill and needs no edit. `plugins/knowledge/.../choosing-a-format.md:150` refers to `delivery-design:decision-record`, which survives unchanged — verify it still resolves rather than editing it.

### Verifying nothing was missed

```bash
# 1. No old skill name survives anywhere
grep -rn -E '\b(product-brief|domain-doc|feature-request|feature-brief|feature-design|feature-plan|feature-tickets|feature-publish)\b' \
  --include='*.md' --include='*.json' . | grep -v '^./.git/'
# Expected: zero hits. Allow only deliberate historical mentions in KNOWN-ISSUES.md.

# 2. Directory name matches frontmatter name for every skill
for f in plugins/*/skills/*/SKILL.md; do
  d=$(basename $(dirname "$f"))
  n=$(awk -F': *' '/^name:/{print $2; exit}' "$f")
  [ "$d" = "$n" ] || echo "MISMATCH: $d vs $n"
done

# 3. Every template and prompt path referenced from a SKILL.md resolves
grep -rn 'prompts/\|template.md\|templates/\|references/' plugins/delivery-design/skills/*/SKILL.md
```

---

## Descriptions — the trigger surface

The two-word names remove most of the collision risk that made this the biggest concern. What remains is handled in the description wording.

| Name | Residual risk | Handling |
|---|---|---|
| `backlog-refinement` | "refine this" as a bare phrase | Negative trigger: "Not for refining prose or notes." |
| `request-triage` | Low — the compound is specific | Anchor on *request / idea / backlog / route* |
| `requirements-discovery` | Low | Anchor on *elicitation, stakeholders, current state* |
| `acceptance-review` | Low; slight overlap with code review | Anchor on *work order, acceptance criteria, close out* |
| All others | Distinctive | Standard trigger list |

**No old skill names appear as triggers** (your call: hard rename, nothing else). Artefact-level phrases like "write a PRD" and "create a tech spec" *are* kept — those are genuine natural-language triggers describing what the skill does, not back-compat aliases for a retired name.

Draft descriptions for the four new or highest-risk skills:

> **request-triage** — Triage an incoming request — a feature idea, a bug report, a change ask — and choose which delivery route it takes: direct (straight to work breakdown), standard (refinement first), or full (discovery and design first). Assesses whether the request is worth building, checks the backlog for duplicates, and names the escalation gate whenever it routes to full. The front door to any piece of delivery work. Triggers: "I have an idea for", "should we build X", "new feature request", "is this worth building", "add this to the backlog", "what route does this take".

> **backlog-refinement** — Refine a backlog item or triaged request into a requirements document: scope, users, functional requirements as user stories, acceptance criteria, and what is explicitly out of scope. Use when a request has been triaged onto the standard or full route and needs specifying before design or breakdown. Triggers: "write a PRD", "refine this backlog item", "backlog refinement", "write the requirements", "scope this feature", "what are the acceptance criteria". Not for refining prose or notes.

> **requirements-discovery** — Elicit requirements and frame the problem before a product or a large piece of work is defined. Covers stakeholder mapping, current-state assessment, and structured elicitation — interviews, observation, document analysis — producing a problem statement and the raw material product definition turns into a vision and backlog. Use at the start of client work, or when the problem itself is not yet agreed. Triggers: "requirements elicitation", "run a discovery", "who are the stakeholders", "what problem are we actually solving", "assess the current state".

> **acceptance-review** — Close out a delivered work order: verify the built result against its acceptance criteria, promote business rules discovered during the build into the glossary and domain model, promote contested costly-to-reverse choices into decision records, and archive the spent requirements document and technical specification. Use when a work order is built, before it is considered done. Triggers: "does this meet the acceptance criteria", "close out this work order", "sign off this work", "verify against the requirements", "what did we learn building this".

Cap every description at roughly 90 words. All 11 load into every session in every project where the plugin is installed; detail belongs in the SKILL.md body.

---

## Route awareness

**Route state lives in a file, not in re-derivation.** On any proceed verdict, `request-triage` creates `docs/work/YYYY-MM-<slug>/work-order.md`:

```yaml
---
route: direct | standard | full
escalation_gates: []        # named gates, required and non-empty when route is full
slug: <kebab-case>
opened: YYYY-MM-DD
status: open | built | accepted
---
```

...followed by the triaged request summary and a paragraph of route rationale. Roughly fifteen lines. Every downstream skill reads it first; every artefact it creates inherits `route` and `slug` in its own frontmatter.

The alternative — each skill re-deriving the route from what it was handed — fails across session boundaries and lets two skills reach different conclusions about the same work. One small file, archived at acceptance, is cheaper.

**Out-of-order invocation.** Every skill gets a short "if you were invoked without your input" clause. The rule: state what is missing, offer to proceed on what is available, do not silently fabricate the missing input. `backlog-refinement` invoked with no work order asks whether to create one and defaults to Route B. `acceptance-review` invoked with no requirements document verifies against the tickets instead and says so.

**`work-breakdown` accepts three input shapes.** This is the one functional change hidden inside a rename. It currently requires a Delivery Plan. It must now handle:

| Route | Input | Behaviour |
|---|---|---|
| Direct | `work-order.md` only | Single story, no epic, no phases |
| Standard | `requirements.md` | Epic plus stories, phases derived from dependency order, no delivery plan |
| Full | `delivery-plan.md` + `tech-spec.md` | Current behaviour, unchanged |

---

## Non-functional requirements

`prd-template.md:49-56` lists Performance / Accessibility / Security / Platform / Integrations *per feature*, and `tech-spec-template.md:61-63,85-87` repeats Accessibility, Performance, and Security again. Most of these are project-wide and get restated — differently, each time — on every feature.

**Fix:** a single durable `docs/product/nfr.md`, written once by `product-definition` from a new `nfr-template.md`, amended by `acceptance-review` only when a feature genuinely shifts the baseline. Both feature templates change to:

```markdown
### Non-Functional Requirements

Baseline: `docs/product/nfr.md` — inherited in full unless listed below.

**Deltas for this feature:**
- [Requirement that differs from baseline, with its number and why it differs]

*None* means the baseline applies unchanged.
```

This also makes the third escalation gate checkable, as noted above.

---

## Agent-facing output quality

Tickets and tech specs are read by coding agents. Assessed against the four criteria:

| Criterion | Current state | Fix |
|---|---|---|
| **File-level boundaries** | Absent. `tech-spec-template.md` lists components by name only; `story-template.md:33` tasks are prose — "Add LikeButton component to CommentCard" — with no path anywhere. | New `## Files & Boundaries` section in `tech-spec-template.md`: paths to create, paths to modify, paths not to touch. New `**Files:**` block in `story-template.md`. `architect.md`, `frontend.md`, `backend.md` prompts updated to emit paths. |
| **Machine-checkable acceptance criteria** | Partial. Given/When/Then is present and `qa.md` asks for precision, but nothing binds a criterion to a check that can be run. | Every criterion gains a `Verify:` line naming a command or test identifier. `qa.md` updated to require it and to reject criteria it cannot bind to a check. |
| **Explicit verification instruction** | Absent. `story-template.md:63-69` Definition of Done says "Tests are written and passing" without naming a command. | New `## Verification` block in `story-template.md` carrying the exact commands, sourced from the `AGENTS.md` commands block. |
| **Enough "why"** | Partial. Description and Technical Context are good, but `epic-template.md:17-21` links the PRD, tech spec, and delivery plan — all three of which move to `_archive/` at acceptance, leaving stale links in live tickets. | Tickets carry self-contained *why*. Source links point at durable documents (glossary, domain model, ADRs, `nfr.md`). Transient links are labelled as such. |

---

## New skill specifications

Both follow the existing multi-agent pattern: read prompt file → spawn anonymous subagent via the Agent tool with the file contents as system prompt → never use a globally registered agent → main agent synthesises. Model `claude-sonnet-4-6` throughout, matching the existing skills.

### `requirements-discovery`

**Purpose.** Requirements elicitation, stakeholder mapping, current-state assessment, and problem framing, ahead of `product-definition`. The entry point for client work.

**Inputs.** A client brief, a conversation, an existing system, or nothing but a stated frustration.

**Outputs.** `problem-statement.md`, `stakeholders.md`, `current-state.md` — durable, but see the placement note below.

**Placement — answering your question 2.** These are the one output set in the pipeline that should probably *not* live in the code repo. A stakeholder map and current-state assessment for client work carry names, org structure, and commercial context; a code repo may be handed over, forked, or opened up. Recommended behaviour:

1. Check the host's `CLAUDE.md` / `AGENTS.md` for a configured discovery location.
2. If none, **ask once** and offer: a sibling project directory outside the repo, a vault path, or `docs/discovery/` inside the repo.
3. Record the answer in the host's `AGENTS.md` so it is asked once per project, not once per run.
4. If the chosen target is inside the code repo, warn about stakeholder names before writing.

This is the strongest case in the plugin for the "skills never hardcode where a file goes" doctrine, so it is worth implementing properly here rather than defaulting to `docs/`.

**Subagents — two, in parallel:**

| Prompt | Role | Produces |
|---|---|---|
| `prompts/business-analyst.md` | Senior BA, BABOK-framed elicitation | Elicitation plan by technique (interview, observation, document analysis, workshop); stakeholder map on interest × influence with a named concern each; current-state assessment covering systems, processes, pain points; candidate problem statement |
| `prompts/challenge-lens.md` | Adversarial framer | Attacks the framing: is the stated problem the real one, whose problem is it, what evidence exists, what happens if nothing is done, what is being assumed. Returns a revised problem statement or a defence of the original |

Main agent reconciles the two, presents the problem statement for confirmation before writing, writes the three files, and advises running `product-definition`. Question cap: 7, matching the existing skills.

### `acceptance-review`

**Purpose.** The closing step. Verify, harvest, archive.

**Inputs.** `docs/work/YYYY-MM-<slug>/` (work order, requirements, tech spec, tickets) and the repository itself.

**Outputs.** A verification report; additions to `glossary.md`, `domain-model.md`, `nfr.md`; new decision records; removal of the `AGENTS.md` entry; the work folder moved to `_archive/`.

**Scope.** One run per work order, after the last story is done (your question 3). Not per story — that would fragment the harvest and make the archive step incoherent.

**Subagents — two, in parallel:**

| Prompt | Role | Produces |
|---|---|---|
| `prompts/verification-lens.md` | QA verifier; needs repo read and Bash to run verification commands | Per-criterion `PASS` / `FAIL` / `UNVERIFIABLE`, each with evidence: a file path, a test name, or command output. Never infers a pass from the absence of a failure |
| `prompts/harvest-lens.md` | Documentation harvester | Candidate glossary terms with definitions; candidate domain entities, relationships, invariants; candidate decision records with `decision-record`'s three significance tests pre-applied and the failing test named where a candidate does not clear |

**Main agent sequence:**

1. Read the work order. Refuse cleanly if `status` is already `accepted`.
2. Run both lenses in parallel.
3. Present the verification result. **On any `FAIL` or `UNVERIFIABLE`, stop.** Report what failed and what would close it. Do not harvest, do not archive.
4. On full pass, present harvest candidates for confirmation. Nothing is written without agreement.
5. Invoke `domain-modelling` in append mode for confirmed glossary and domain-model additions. Invoke `decision-record` for each confirmed decision. **Do not restate either format** — those skills own their shapes, and restating them is exactly the duplication `docs/skill-authoring.md:9` forbids.
6. Amend `docs/product/nfr.md` if a delta became the new baseline.
7. Remove the work order's `AGENTS.md` entry.
8. `git mv` the work folder to `docs/work/_archive/<slug>/`, stamp `status: superseded` / `archived:` / `harvested_to:` on each file, set the work order's `status: accepted`, and print the `rm -rf` command for you to run manually.

---

## Phasing

Nine commits. Each is independently reviewable and revertible, and none leaves the plugin in a non-installing state.

| # | Phase | Contents | Verified by |
|---|---|---|---|
| 0 | Plan | Write `docs/delivery-design-rework.md` | — |
| 1 | **Mechanical rename** | 8 `git mv`s; `name:` frontmatter; H1s; all ~90 cross-references in SKILL.md bodies, READMEs, templates, prompts, repo docs. No behaviour change. | Greps 1 and 2 |
| 2 | **Descriptions** | Rewrite all 9 renamed `description:` fields around the new names and triggers. Own commit so a trigger regression bisects to exactly one change. | Trigger smoke test |
| 3 | **Routing** | `request-triage/references/routes.md`; route selection + `work-order.md` in `request-triage`; route-awareness and out-of-order clauses in every skill; `work-breakdown` accepts three input shapes | Dry-run all three routes |
| 4 | **Artefact locations** | `docs/work/YYYY-MM-<slug>/` and `_archive/` conventions; legacy `docs/features/` read fallback; host-override line in every skill; `AGENTS.md` step moves from `technical-design` to `work-breakdown` | Dry-run in a repo holding legacy `docs/features/` |
| 5 | **NFR home** | `nfr-template.md`; `product-definition` writes `nfr.md`; PRD and tech-spec templates reference rather than repeat | Inspect a generated requirements doc |
| 6 | **Agent-facing templates** | Files & Boundaries; `Verify:` lines; Verification block; durable-only source links; matching prompt updates in `architect.md`, `frontend.md`, `backend.md`, `qa.md` | Agent-executability check |
| 7 | **`requirements-discovery`** | `SKILL.md`, `README.md`, 2 prompts, 3 templates, placement negotiation | Run cold on a described problem |
| 8 | **`acceptance-review`** | `SKILL.md`, `README.md`, 2 prompts, report template | Run against a completed Route B work order, then a deliberately failing one |
| 9 | **Docs and version** | Repo `README.md` (roster, both diagrams, "9"→"11", the `idea → PRD` header line); plugin `README.md`; `marketplace.json` and `plugin.json` descriptions; `plugin.json` `1.0.1` → `2.0.0`; `docs/skill-authoring.md:13` and `note-formats/SKILL.md:67` examples; a Cowork-rename note in `KNOWN-ISSUES.md`; delete `docs/delivery-design-rework.md` | Reinstall and confirm 11 skills load |

Phases 1 and 2 must land before 3–8, which are all written in the new names. Phase 4 must land before 8, since `acceptance-review` archives into the structure Phase 4 defines.

`plugin.json` goes to `2.0.0`: the rename is breaking. Both descriptions must be updated in step with the roster, per `docs/plugin-mechanics.md:13-15`.

---

## Risks

| Risk | Severity | Mitigation |
|---|---|---|
| **Cowork stale-sync leaves both skill sets installed.** `KNOWN-ISSUES.md` documents remote plugin state overwriting local. After a rename an install may carry `feature-brief` *and* `backlog-refinement` at once, competing for the same triggers. | High | Reinstall the plugin in Cowork after Phase 9 (remove/re-enable is the only force-resync lever). Note it in `KNOWN-ISSUES.md`. Verify Claude Code separately — it has no remote-sync layer. |
| **Trigger regression.** Reduced substantially by the two-word names, but still the change most likely to be felt day to day. | Medium | Phase 2 is its own commit. Negative trigger in `backlog-refinement`. Smoke test run immediately after Phase 2. |
| **In-flight work orphaned.** Existing repos hold `docs/features/<slug>/`. | Medium | Phase 4 adds a legacy read fallback. No automatic migration — existing work finishes where it is. |
| **`work-breakdown` regression.** It gains two new input shapes; the existing Route C path could break. | Medium | Phase 3 dry-runs all three routes, Route C first against a known-good delivery plan. |
| **Archive read as current.** A superseded spec in `_archive/` found by an agent and treated as live. | Medium | `status: superseded` frontmatter on every archived file; `_archive/README.md`; `AGENTS.md` never points into `_archive/`; you delete manually once satisfied. |
| **Description budget.** 11 descriptions load in every session in every project. | Medium | ~90-word cap; detail moves into SKILL.md bodies. |
| **`acceptance-review` moves files on a false pass.** | Low — downgraded by archiving | Fully reversible with `git mv`. Still gated on all criteria passing and explicit confirmation; never runs on FAIL or UNVERIFIABLE. |
| **Cross-plugin reference.** `note-formats/references/choosing-a-format.md:150` points at `delivery-design:decision-record`. | Low | That name is unchanged. Verify it still resolves after Phase 1 rather than editing it. |

---

## Open questions

Settled during planning: `delivery-planning` over `release-planning` (1); acceptance runs per work order (3); `requirements.md` lives in the named work folder (4); `request-triage` keeps the backlog append on a Park verdict (5); `build` stays a plain-text marker with no skill and no cross-plugin reference (6).

Remaining:

**A. Where do `requirements-discovery` outputs live?** Recommendation above is *ask once per project and record the answer*, with a warning when the target is inside the code repo. The decision you may want to make up front is what the *offered default* should be — a sibling directory outside the repo is my recommendation for client work, `docs/discovery/` for your own products. Confirm at Phase 7 rather than now.

**B. Does `product-definition` still emit `vision.md`, or should the filename follow the skill name?** The current file is `docs/product/vision.md` while the skill was `product-brief` — already misaligned. Recommendation: keep `vision.md`. It is the more useful name for the reader, and unlike `prd.md` it is not competing with a directory convention.

---

## Verification

**After Phase 1 (rename):** run greps 1 and 2. Zero hits, zero mismatches.

**After Phase 2 (descriptions) — trigger smoke test.** In a fresh session with the plugin installed, confirm each phrase fires the intended skill and nothing else:

| Phrase | Should fire |
|---|---|
| "I've got an idea for a feature" / "should we build this?" | `request-triage` |
| "write a PRD for this" / "refine this backlog item" | `backlog-refinement` |
| "create a tech spec" / "how should we build this technically" | `technical-design` |
| "what order do we build these in" | `delivery-planning` |
| "break this into stories" | `work-breakdown` |
| "push these to Linear" | `ticket-publish` |
| "who are the stakeholders" / "what problem are we solving" | `requirements-discovery` |
| "does this meet the acceptance criteria" / "close out this work order" | `acceptance-review` |
| "write the glossary" / "model the domain" | `domain-modelling` |
| "tidy up the wording in this note" | **nothing** (guards `backlog-refinement`) |
| "triage my inbox" | **nothing** (guards `request-triage`) |
| "publish this artifact" | **nothing** (guards `ticket-publish`) |

**After Phases 3–4 — route dry runs.** In a scratch repo:

- **A:** "fix the typo on the pricing page" → direct route, `work-order.md` with `route: direct`, `work-breakdown` produces one story and no epic.
- **B:** "add CSV export to the reports page" → standard route, `requirements.md` then tickets, no tech spec, no delivery plan.
- **C:** "add Stripe subscriptions" → trips the third-party and costly-to-reverse gates; `request-triage` names both and routes full.
- **Escalation discipline:** a mid-sized request with no gate present must stay on B. If it escalates without a named gate, the routing has failed its purpose.

**After Phase 6 — agent-executability check.** Take one generated story to a fresh agent session with only the repo and that file. It should start work without a clarifying question, and be able to state the command that proves it is done.

**After Phase 8 — acceptance both ways.** Against a completed Route B work order: verification passes, harvest confirmed, folder in `_archive/` with superseded stamps, `AGENTS.md` entry gone, delete command printed. Then against a work order with one deliberately unmet criterion: it must stop at step 3, report the failure, and leave every file in place.

**After Phase 9 — install check.** `/plugin marketplace add` and `/plugin install delivery-design` in Claude Code. Confirm 11 skills load under the new names, `plugin.json` reads `2.0.0`, and both plugin descriptions match the roster. Then reinstall in Cowork and confirm no old-named skills survive the sync.
