# product-delivery v4: the work order is the map

## Context

Eleven skills, three routes, and no way to tell from inside a piece of work which skill comes next. The fix is to make the work order carry its own route as a checklist (`## Steps`), so the only rule to remember is **every piece of work starts with `request-triage`**. Separately, `work-breakdown` writes Epic and Story files that go unused in practice. It switches to one `PLAN.md` of vertical slices per work order, which is the shape agents actually execute well. The docs layout moves to three lenses (`architecture/`, `domain/`, `product/`) plus a committed `work/` folder keyed by tracker-style IDs. Version 4.0.0, because the paths break.

Nothing here adds, removes or renames a skill. `engineering` and `knowledge` are untouched.

---

## 1. Files

Paths are relative to `plugins/product-delivery/` unless they start with `.claude-plugin/`, `README.md` or `docs/` at repo root. `S/` = `skills/`.

### Create

| File | Why |
|---|---|
| `docs/plans/product-delivery-v4.md` (repo root) | This plan, as you asked |
| `S/work-breakdown/templates/plan-template.md` | The new `PLAN.md` shape (skeleton in §6) |

### Delete

| File | Why |
|---|---|
| `S/work-breakdown/templates/epic-template.md` | Retired. Keeps: durable-references list, working-document list → plan Context |
| `S/work-breakdown/templates/story-template.md` | Retired. Keeps: Files table + Do not touch, "enough why", AC with `Verify:`, Verification block, manual-check rule → slice fields and Check block |

No renames. (`templates/` keeps one file; flattening it to `plan-template.md` beside `agents-template.md` would be tidier but is churn. Leave it.)

### Edit

| File | Phases | What changes |
|---|---|---|
| `S/request-triage/references/artefacts.md` | 1, 2 | New tree; owns the **ID rule**; legacy-read rules for v3 paths (`docs/work/YYYY-MM-<slug>/`, `docs/product/glossary.md`, `docs/decisions/`) and pre-v2 `docs/features/`; archive stamp paths. Phase 2 swaps `tickets/` for `PLAN.md` |
| `S/request-triage/references/routes.md` | 1, 3 | Gate checks point at `docs/domain/glossary.md`, `docs/domain/context-map.md` (if present); work-order path and `id:` frontmatter. Phase 3: canonical **Steps per route**, Route C reordered to start at triage, the Steps protocol (read, tick, name next, out-of-order rule) replacing the current "Invoked out of order" section |
| `S/request-triage/SKILL.md` | 1, 3 | Paths, duplicate scan, ID derivation (5b), hand-off. Phase 3: fill Steps; hand-off names the first unticked step; status mode on an existing work order (see Q6) |
| `S/request-triage/work-order-template.md` | 1, 3 | `id:` in frontmatter, Phase 3: `## Steps` |
| `S/request-triage/prompts/product-lens.md` | 3 | "Too broad / new product" stops recommending Park-then-`product-definition`; it recommends Build now, Full, product bootstrap (Q5) |
| `S/request-triage/README.md` | 1, 3 | Output path, Steps |
| `S/work-breakdown/SKILL.md` | 1, 2, 3 | Paths, then full rewrite of Steps 1–5 and 7 around `PLAN.md`; Step 6 (`AGENTS.md`) points at `PLAN.md` and creates the `CLAUDE.md` symlink. Phase 3: tick + next |
| `S/work-breakdown/agents-template.md` | 1, 2 | New paths; architecture docs; "current work → its `PLAN.md`, one slice at a time" |
| `S/work-breakdown/README.md` | 2 | Output, inputs, how it works |
| `S/ticket-publish/SKILL.md`, `README.md` | 1, 4 | Path swap in 1; rewrite in 4 |
| `S/acceptance-review/SKILL.md`, `README.md` | 1, 2, 3 | Paths and `_archive/WO-NNNN-<slug>/`; reads `PLAN.md` (legacy `tickets/` fallback); ticks its Steps line, sets `status: accepted` |
| `S/acceptance-review/acceptance-report-template.md`, `archive-readme-template.md`, `prompts/harvest-lens.md`, `prompts/verification-lens.md` | 1, 2 | Durable paths; criteria source is `PLAN.md` slices |
| `S/backlog-refinement/SKILL.md`, `README.md` | 1, 3 | Paths; hand-off; tick + next |
| `S/technical-design/SKILL.md` | 1, 3 | Paths; Step 2 reads `docs/architecture/tech-stack.md` and `conventions.md` before asking for constraints; tick + next |
| `S/technical-design/prompts/qa.md`, `tech-spec-template.md` | 2 | "acceptance criteria in the tickets" → "in the `PLAN.md` slices"; Verification Commands "copied into each slice's Check" |
| `S/delivery-planning/SKILL.md`, `README.md`, `delivery-plan-template.md` | 1, 2, 3 | Paths; hand-off text is already wrong ("create the work item tickets in your project management tool"): fix to "`work-breakdown` turns this into `PLAN.md`"; tick + next. Internal "story" vocabulary left alone (Q7) |
| `S/domain-modelling/SKILL.md`, `README.md` | 1, 3 | `docs/domain/glossary.md`, `docs/domain/domain-model.md`; `context-map.md` named as optional, hand-written; tick + next when listed |
| `S/decision-record/SKILL.md`, `README.md` | 1 | Output path by scope (Q2) |
| `S/product-definition/SKILL.md`, `README.md` | 1, 3 | Paths (vision, backlog, nfr stay in `docs/product/`); tick + next when listed |
| `S/requirements-discovery/SKILL.md` | 3 | Tick + next when listed. Location logic unchanged |
| `README.md` (plugin) | 5 | First line, mental model, routes, roster, layout, what closes the loop |
| `README.md` (repo root) | 5 | Design principle wording, roster rows for `work-breakdown` / `ticket-publish`, Status note for 4.0 |
| `.claude-plugin/plugin.json` (plugin) | 5 | `version: 4.0.0`. Description unchanged: still accurate |
| `.claude-plugin/marketplace.json` (root) | none | Carries no version. Description unchanged, still matches `plugin.json` |

---

## 2. Phases, one commit each

I've swapped your phases 2 and 3: the Steps lines name `PLAN.md`, so `PLAN.md` should exist first. Commits stay local until phase 5 lands, then one push (with your go-ahead). Each commit leaves its own scope coherent. Honest caveat on "independently revertible": phases 1–3 touch some of the same lines, so reverting phase 1 *alone* after phase 3 will conflict. Reverting in reverse order is always clean, and you can stop after any phase.

**0. Plan.** Copy this file to `docs/plans/product-delivery-v4.md`. Commit: `Add product-delivery v4 plan`.

**1. Layout defaults.** Every path in §1 marked phase 1. `artefacts.md` gets the new tree (with `tickets/` still in the work folder; phase 2 swaps it), the ID rule, and the legacy-read rules. Out: epic/story templates (deleted next phase). Commit: `Move product-delivery to the architecture/domain/product layout`.

**2. PLAN.md.** New `plan-template.md`, delete epic/story templates, rewrite `work-breakdown`, repoint `acceptance-review`, verification-lens, qa prompt, tech-spec template, delivery-planning hand-off. `ticket-publish` is knowingly stale until phase 4 (nothing installs from an unpushed commit). Commit: `Replace epic and story files with a per-work-order PLAN.md`.

**3. Steps.** `## Steps` in the template, canonical per-route Steps and protocol in `routes.md`, Route C reordered, each route skill reads/ticks/names next, `PLAN.md` How to run ticks `build`, `acceptance-review` ticks the last line. Commit: `Make the work order carry its route as a Steps checklist`.

**4. ticket-publish.** Rewrite (§5). Commit: `Publish PLAN.md slices to Linear or GitHub Issues`.

**5. README and version.** Both READMEs, `plugin.json` → 4.0.0. Commit: `Update product-delivery docs and bump to 4.0.0`.

---

## 3. Key mechanics

### ID rule (owned by `artefacts.md`)

Every work order is `WO-NNNN`, zero-padded to four digits: the highest `WO-` number across `docs/work/` **and** `docs/work/_archive/`, plus one (same logic `decision-record` uses for `NNNN`). Folder: `docs/work/WO-NNNN-<slug>/`. Frontmatter gains `id:`, and `source:` when the request arrived as a tracker issue (`source: MIT-42`). `opened:` stays.

The only clash possible is a tracker whose own team key is `WO`, so recommend three-letter team keys.

**Branch names**, also owned by `artefacts.md`: a branch carrying one published slice takes that slice's issue ID (Linear links pull requests by the ID in the branch name); every other branch takes the WO ID, and a branch carrying several published slices lists each as `Fixes <issue>` in its pull request. `PLAN.md` carries the resolved name in a `Branch:` line, filled by `work-breakdown` and updated by `ticket-publish`, because the agent executing a plan may not have the plugin installed.

### Steps (owned by `routes.md`)

Direct:
```markdown
## Steps
- [ ] work-breakdown → PLAN.md
- [ ] build (see PLAN.md, How to run)
```
Standard: exactly your example. Full feature (gates named):
```markdown
## Steps
- [ ] domain-modelling → docs/domain/glossary.md, domain-model.md   (only if the new-concepts gate opened)
- [ ] backlog-refinement → requirements.md
- [ ] technical-design → tech-spec.md
- [ ] delivery-planning → delivery-plan.md
- [ ] work-breakdown → PLAN.md
- [ ] build (see PLAN.md, How to run)
- [ ] acceptance-review
```
Full product bootstrap: see Q5.

Protocol, stated once in `routes.md` and as a fixed three-line block in each route skill's `SKILL.md` ("Before you start" / "When you finish"):
- Read `work-order.md` if one exists.
- If an earlier line is unticked, name it and ask whether to proceed. Never fabricate its output.
- On finishing, tick your own line and end with "Next: `<first unticked step>`".
- No work order: say so, offer `request-triage`. Do not create one.
- `ticket-publish` and `decision-record` tick a line only if the user added one; they never add one.

That three-line block repeats across nine `SKILL.md` files, which brushes against "a convention in two skills is a bug". I'm doing it deliberately: the definition lives in `routes.md`, but a behaviour the model must perform on every run is unreliable as a pointer alone (`skill-authoring.md` already says dependencies must be stated in `SKILL.md`). The block says *what to do*; `routes.md` keeps the *why* and the per-route lists.

`build` is ticked by the executing agent: `PLAN.md` How to run says so after the last slice's Check passes.

---

## 4. work-breakdown → PLAN.md

- Input by route stays as now (work order / requirements / delivery plan + tech spec). Direct gets a single-slice plan with `review: stop`.
- Step 2 presents the slice list (goal, review mode, depends-on) for confirmation before writing. Proposed review default: `stop` for slice 1 and any slice touching a data model, public API, auth or money; `continue` for mechanical slices. You override in review.
- Sizing: each slice fits one agent session and leaves the build green. More than ~8 slices, or slices that could merge independently → recommend splitting the work order (back through `request-triage` for the new ID), before writing anything.
- Full: each item in the delivery plan's Full Story List becomes a slice; two that cannot ship apart merge. The delivery plan's phases are a natural split line if the size rule trips.
- Existing `PLAN.md` → ask overwrite or cancel.
- Kept from the story template: do not invent AC on Standard/Full (say when you add one); Files from the tech spec's Files & Boundaries or the codebase, and "if you cannot name the files, the slice is not ready"; Check commands copied verbatim from the tech spec or `AGENTS.md`, never guessed; every AC has `Verify:` or `Verify: manual — …`; link only durable docs.
- Step 6 (`AGENTS.md`): entry points at `docs/work/WO-NNNN-<slug>/PLAN.md`. When creating `AGENTS.md`, also `ln -s AGENTS.md CLAUDE.md` if no `CLAUDE.md` exists; if a real `CLAUDE.md` exists, leave it and say so.

**Description rewrite (draft):**
> Break specified work into a PLAN.md for one work order: numbered, dependency-ordered vertical slices a coding agent executes one at a time, each with its files, acceptance criteria and exact check commands. The slices are the tickets, reviewed locally before anything reaches Linear or GitHub. Triggers: "break this into stories", "break this into tickets", "create the tickets", "write up the tasks", "generate work items", "work breakdown", "slice this work", "write the PLAN.md". Run ticket-publish afterwards to mirror the slices into a tracker.

Triggering risk: keeps every current trigger phrase, so "break this into tickets/stories" should still land. I deliberately avoid bare "write a plan" / "make a plan": it collides with plan mode and with `delivery-planning` ("plan the delivery", "break this into phases"). "Create the tickets" overlaps `ticket-publish` today already; landing on `work-breakdown` is the safe default (local first). Verified by smoke test (§7).

---

## 5. ticket-publish

- Source: `docs/work/WO-NNNN-<slug>/PLAN.md` (+ `work-order.md` for title and ID). No plan → offer `work-breakdown`; no work order → offer `request-triage`.
- **Linear**: ask Project or parent issue (default per Q4). Each slice → issue: title `Slice N: <goal>`, body = goal, AC with `Verify:`, link to `PLAN.md` on the default branch (`https://github.com/<owner>/<repo>/blob/<default>/docs/work/WO-NNNN-<slug>/PLAN.md`, resolved from `git remote` and `git symbolic-ref refs/remotes/origin/HEAD`; if unresolvable, ask). `blocked by` relations from slice order/depends-on. "Issue type Epic" instruction removed.
- **GitHub Issues**: work order → milestone; slice → issue in that milestone; blocking via native relation if available, else a "Blocked by #n" line.
- Write-back: each slice's `**Issue:**` line in `PLAN.md` gets the ID/URL; `PLAN.md` frontmatter gets `tracker:` (project/milestone/parent). Offer to commit that edit; never push.
- **Route A**: one issue for the whole work order (title from work order, body = the single slice). If the work order has a `source:` issue, update that issue with the plan link instead of creating a duplicate.
- The Linear Project or GitHub milestone description links the work order folder on the default branch.
- Re-run safety: slices with an `Issue:` already set are skipped, which fixes the current "re-running may create duplicates" warning.
- Confirmation before any create stays.

**Description rewrite (draft):**
> Publish a work order's PLAN.md to Linear or GitHub Issues: the work order becomes a Linear project or parent issue (a GitHub milestone), each slice an issue with its goal, acceptance criteria and a link back to the plan, and the created IDs are written back into PLAN.md. A Direct-route work order, such as a bug fix, publishes as a single issue. Use after work-breakdown, once the plan is reviewed. Triggers: "publish the tickets", "push these to Linear", "create GitHub issues from the plan", "upload the tickets". Not for publishing artifacts, notes, packages, or posts.

Triggering risk: low. I've left out "put this bug in Linear": it would let a raw bug bypass `request-triage`.

**Other description touches:** `request-triage` adds "every piece of work starts here" and "writes the work order with its ID and a Steps checklist of which skills run, in order", plus triggers "where do I start", "what's next on this work order" (risk: mild overlap with any skill a user names directly; acceptable, since an explicit skill name wins). `acceptance-review`: "archive the spent requirements document and technical specification" → "archive the work order folder". No other descriptions change.

---

## 6. PLAN.md template skeleton (generic)

```markdown
---
id: WO-NNNN
slug: <slug>
route: direct | standard | full
status: draft | in-progress | built
tracker:            # set by ticket-publish
---

# WO-NNNN: <Title>

## How to run this

Read this before starting. It overrides default working habits.

**Branch:** `WO-NNNN-<slug>` (resolved by work-breakdown; see Branch names)

1. One slice at a time, in order. Not two because the second looks small.
2. Run the slice's Check before stopping. If it fails, fix it inside the slice.
   If it fails twice, stop and report what ran, what failed, what you tried.
3. Never improvise a substitute, never skip ahead, never start the next slice to fix this one.
4. `review: stop`: after the Check, do not commit or stage. Show `git diff` and `git status`, say which slice is next, and wait.
   `review: continue`: commit the slice as `WO-NNNN slice N: <goal>` and carry on.
5. Cloud or background agent: treat every slice as `continue`, work on the branch named above, and end with one PR titled `WO-NNNN: <Title>`, with `Fixes <issue>` for each published slice.
6. After the last slice's Check passes, tick `build` in `work-order.md`.

## Context
Why this work exists, what it builds on, and anything a judgement call will need.
Durable references: glossary, domain model, nfr, decision records that apply.

**Goal:** one sentence.
**Done when:** observable conditions, each checkable.
**Out of scope:** (omit if nothing)

## Slices

### Slice 1: <goal>
`review: stop` · **Issue:** –

Why: one or two sentences, enough for a judgement call the tasks did not anticipate.

**Files**
- Create: `path`
- Modify: `path`

**Do not touch:** `path`, and why.

**Tasks**
- [ ] …

**Acceptance criteria**
- [ ] Given …, when …, then …
      Verify: `command or test name`

**Check**
```bash
<exact commands>
```

## Open questions
(omit if none)
```

Example content will be invented and generic (a CSV export, say). Nothing from any private repo.

---

## 7. Verification

Run after each phase (scoped to that phase's files) and in full after phase 5. Expected count is zero unless noted.

```bash
P=plugins/product-delivery
grep -rnE 'YYYY-MM-[^D]|YYYY-MM-slug' $P                         # old folder scheme (YYYY-MM-DD dates are fine)
grep -rn  'docs/decisions' $P                                     # decisions moved
grep -rnE 'docs/product/(glossary|domain-model)' $P               # moved to docs/domain (legacy section in artefacts.md excepted)
grep -rnE 'tickets/|_epic|epic-template|story-template|ticket_type|depends_on:' $P   # legacy notes in artefacts.md + acceptance-review excepted
grep -rnE '\b[Ee]pic\b' $P README.md
grep -rn  'docs/features' $P                                      # only artefacts.md legacy section + fallback lines
grep -rnE '[Ss]tor(y|ies)' $P | grep -v 'user stor'               # review each: delivery-planning internals allowed (Q7)
grep -rnE '<private project names>|\.work/' $P docs README.md   # leakage: names kept out of this file on purpose
```

Plus:
- **Relative links resolve:** script over every `` `../<skill>/<file>` `` and `references/…` mention, checking the file exists.
- **Skill names valid:** every backticked kebab-case name followed by "skill" or in a Steps line matches a directory from `find $P/skills -name SKILL.md`.
- **Frontmatter parses:** `ruby -ryaml` load of every `SKILL.md` frontmatter (no PyYAML here); descriptions under 1024 chars.
- **Version:** `plugin.json` reads `4.0.0`; descriptions in `plugin.json` and `marketplace.json` identical.
- **Trigger smoke test** (manual, needs you): in a scratch repo, `/plugin marketplace add <path to this repo>`, install, then try "break this into tickets", "break this into stories", "publish the tickets", "plan the delivery", "I have an idea for X". Confirm each lands on the expected skill.
- **Dry run** (manual): Standard route on a toy request in the scratch repo: triage → refinement → breakdown, checking ID, Steps ticking and next-step naming.

---

## 8. Open questions, with recommendations

**Q1. Where does `nfr.md` belong?** Recommend **`docs/product/`**, as drafted. The numbers in it (accessibility standard, browser support, latency budget) are commitments to users, `product-definition` asks for them, and the escalation gate treats a delta against it as a product requirement. `technical-design` reads it either way. Counter-case: it is consumed mostly by technical skills. Not strong enough to move it.

**Q2. Where do product and process decision records go?** Recommend: architecture → `docs/architecture/decisions/`, product → `docs/product/decisions/`, process → `docs/architecture/decisions/` with `scope: process` in frontmatter (already a template field). Each lens owns its decisions, no new top-level folder, and in a code repo the process decisions that clear the gate (branching, CI, review policy) sit with conventions. Numbering per folder. Alternative: a single `docs/architecture/decisions/` for all scopes; simpler, but buries product decisions in the technical lens.

**Q3. Route A bugs: open a folder, or straight to the tracker?** With a tracker connected, `request-triage` offers both on Direct: a single tracker issue with no work order, for a bug that needs nothing more, or a `WO-NNNN` folder with a one-slice `PLAN.md` a cloud agent can run. Without a tracker it opens the folder. Either way the work started at `request-triage`. Route A skips `acceptance-review`, so a Route A folder is archived by the executing agent as the last step of How to run (`git mv` to `_archive/`, `status: done`). No extra skill.

**Q4. Linear Project or parent issue?** Project is the default: one work order becomes a Linear project with one issue per slice. Parent issue is the option, with each slice as a sub-issue; when the work order has a `source:` Linear issue, that issue is offered as the parent rather than creating another.

**Q5. Route C and "triage first".** Route C currently starts at `requirements-discovery`, before triage. Recommend: triage first everywhere, and split Full into two shapes that `request-triage` chooses between:
- *Full feature*: Steps as in §3.
- *Product bootstrap* (no `vision.md`, or the lens judges it a new product): Steps are `requirements-discovery` (if the problem is not agreed) → `product-definition` → `domain-modelling` → "triage each Must Have (each becomes its own work order)". No `PLAN.md`, no `acceptance-review`; the work order closes when the product docs exist.

This needs the product-lens prompt change in §1. It's the one place where the change reaches past wording. If you'd rather not, the fallback is to leave product bootstrap outside the work-order system and say so in the README.

**Q6. `request-triage` status mode.** When pointed at an existing work order, report Steps and the next step instead of re-triaging. A few lines, and it answers "where was I?" directly. Recommend yes. Drop it if it feels like scope creep.

**Q7. Delivery-plan vocabulary.** `delivery-planning` still says "stories" and "Full Story List". Recommend leaving it for now and having `work-breakdown` map stories to slices. Renaming the template and four planner prompts is a later, separate change.

**Q8. Legacy projects.** Existing v3 projects have `docs/product/glossary.md` and `docs/decisions/`. Recommend: read new path first, fall back to old, say which, never migrate unprompted, and offer a one-off `git mv` list. Same rule the plugin already applies to `docs/features/`.

---

## Parked (not in this change)

- Flattening `work-breakdown/templates/` to a single file.
- Renaming delivery-plan "stories" to "slices" (Q7).
- A skill or checker that validates a project's `docs/` against the layout.
