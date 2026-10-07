---
id: WO-NNNN
slug: [slug, from the work order]
route: direct | standard | full
status: draft | in-progress | built
tracker:
---

# WO-NNNN: [Title]

## How to run this

**Read this before starting. It overrides any default working habit.**

[N] slices, numbered from 1, ordered by dependency. Each one leaves the build green.

**Branch:** `[branch name]`

1. **Do one slice at a time, in order.** Not two because the second looks small.
2. **Run the slice's Check before stopping.** If it fails, fix it inside the same slice. If it fails twice, stop and report what you ran, what failed, and what you tried.
3. **Never improvise a substitute and never skip ahead.** If a slice turns out to be wrong or blocked, stop there and say so. Do not start the next slice to work around this one.
4. **Each slice names its review mode.**
   - `review: stop`: after the Check passes, do not commit or stage. Show the diff (`git diff`, plus `git status` for new or deleted files), say which slice is next, and wait for the author to review and commit.
   - `review: continue`: after the Check passes, commit the slice as `WO-NNNN slice N: [goal]` and carry on to the next.
5. **Running in a cloud or background agent,** treat every slice as `review: continue`. Work on the branch named above, and end with one pull request titled `WO-NNNN: [Title]` that links this file and, for each slice with an issue, says `Fixes [issue]`.
6. **When the last slice's Check passes,** tick `build` in the Steps of `work-order.md`, and set this file's `status: built`.
7. **[Direct route only] Then archive the work folder,** because no review step will: delete this work order's line from `AGENTS.md`, set `status: done` in `work-order.md`, and `git mv docs/work/WO-NNNN-[slug] docs/work/_archive/WO-NNNN-[slug]`. Under `review: stop`, leave the move uncommitted with the rest of the slice.

## Context

[Why this work exists and what it builds on. Write enough *why* that someone hitting a problem no task anticipated can work out what the plan would have wanted. "Keep the list order stable while a save is in flight, because users lose their place otherwise" lets someone solve a new problem; "use optimistic updates" does not.]

[The requirements document and tech spec are archived when the work is accepted, so carry the context here rather than linking to them.]

**Durable references:** [The documents that outlive this work and apply to it: `docs/domain/glossary.md`, `docs/domain/domain-model.md`, `docs/product/nfr.md`, any decision record this work depends on. Omit any that do not apply.]

**Goal:** [One sentence: the outcome this work order delivers.]

**Done when:** [Observable conditions, each one checkable. "An exported CSV opens in a spreadsheet with one row per order and the column headings from the requirements", not "export works".]

**Out of scope:** [Things a reader might reasonably expect this work to include, and that it does not. Omit if there is nothing.]

---

## Slice 1: [Goal of the slice, as an outcome]

`review: stop` · **Issue:** not published

[One or two sentences: why this slice, and why in this position. Name what it depends on if it is not simply the slice before.]

**Files**

- Create: `[path]`
- Modify: `[path]`

**Do not touch:** `[path]`: [why an implementer might think it is in scope, and why it is not]. Or "Nothing flagged."

**Tasks**

- [ ] [Specific change, e.g. "Add an `exportOrders` server action that streams rows from the orders query"]
- [ ] [Specific change]

**Acceptance criteria**

- [ ] **Given** [context], **when** [action], **then** [outcome]
      **Verify:** `[command or test name]`
- [ ] **Given** [context], **when** [action], **then** [outcome]
      **Verify:** manual — [exactly what a person does, and what they should see]

**Check**

```bash
[exact commands, copied from the tech spec or AGENTS.md, in the order to run them]
```

---

## Slice 2: [Goal]

[Same shape as slice 1.]

---

## Open questions

[Anything unresolved that a slice depends on, and which slice it blocks. Omit the section if there is nothing.]

---

*Written by `work-breakdown`. Mirror the slices into Linear or GitHub Issues with `ticket-publish`; close out with `acceptance-review` once the work is built.*
