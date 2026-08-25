---
name: note-formats
description: The source of truth for note and document shape — which format a piece of content should take, and what that format looks like. Use when deciding what kind of note something should be ("is this an atomic note or an overview?", "should this be a runbook or a playbook?"), when applying or reformatting to a known shape, when splitting a note into several, or when writing any note that needs to match house conventions. Covers atomic notes, overview and hub notes, maps of content, decision guides, guide notes, checklists, runbooks, playbooks, standard notes, and decision records.
---

# Note Formats

Shape only. This skill answers *what does this document look like* — the format taxonomy, the decision flow between formats, and the conventions each one follows.

It does not decide **where a note goes**. Folder structure, placement rules, and filename conventions are vault-specific and belong to the host vault's own `CLAUDE.md` — read that too when writing into a vault.

---

## Step 1 — Determine the shape

If the user named a shape, use it, but sanity-check it against the table. If they did not, classify from the content.

**Knowledge shapes** — read to build understanding.

| Shape | Use for | Voice | Asset |
|---|---|---|---|
| Atomic Note | One self-contained concept | Second person | `assets/atomic-note.md` |
| Overview Note | Introduces a topic area, links out to detail | Second person | `assets/overview-note.md` |
| Hub Note | Groups and contrasts a set of related notes | Second person | `assets/hub-note.md` |
| Decision Guide | Options for a recurring class of choice | Second person | `assets/decision-guide.md` |
| Guide Note | How something works, or how to approach a process | Second person | `assets/guide-note.md` |
| Map of Content | Navigation only — no content of its own | Second person | `assets/map-of-content.md` |

**Operational shapes** — read to get something done.

| Shape | Use for | Voice | Asset |
|---|---|---|---|
| Checklist | Flat items, no sequence, no decisions | Imperative | `assets/checklist.md` |
| Runbook | One task, one deterministic sequence | Imperative | `assets/runbook.md` |
| Playbook | Triage across substantially different procedures | Imperative | `assets/playbook.md` |
| Standard Note | Your settled, opinionated approach to a domain | First person | `assets/standard-note.md` |
| Decision Record | One decision, its context and consequences | Second person | `assets/decision-record.md` |

*Technology Overview* is a variant of Overview Note, not a separate shape — use the Overview Note asset.

**Read `references/choosing-a-format.md`** whenever the shape is not obvious, whenever the content might span two shapes, or before contradicting a shape the user named. It holds the full criteria, the pairs that get confused, and worked examples.

The commonest errors, in order of frequency:

- Reaching for a Playbook when the content is really two or three Runbooks with no triage step between them
- Creating an Overview Note plus a Map of Content plus several atomics for what is genuinely two notes' worth of content
- Writing a Decision Guide when the decision has already been made — that is a Decision Record

### When the content implies more than one document

If the source material covers several distinct things, say so before writing. Name each proposed note, give a one-line description, and state its shape. Ask which to draft now and which to leave as an unresolved link for later.

Do not silently write five notes the user asked for one of.

---

## Step 2 — Apply the shape

1. Read the matching asset in full before writing. It is a skeleton with italicised authoring guidance under each heading — that guidance says when a section should be deleted rather than filled in. Delete it as you go; it must not survive into the finished note.
2. Read `references/obsidian-style.md` for the notation — frontmatter, wikilinks, callouts, headings, emphasis, code blocks, tables. Also holds the substitutions to make when the target is not Obsidian.
3. Read `references/structure-doctrine.md` when splitting, merging, or refactoring notes, when judging how much content a note should carry, or when the material comes from a transcript, book, course, or any other source with a narrative of its own.

Do not invent sections the asset does not have, and do not keep sections the source has nothing to put in. An empty heading is worse than a missing one.

**The note is about the subject, not about where you learned it.** Do not open by narrating the note's own origin, and do not let the source's ordering or emphasis become the note's — see *Source Independence* in `references/structure-doctrine.md`.

That holds for free-standing notes, which are read on their own. Pipeline artefacts are the exception: where a document is a contract with a later stage — a requirements document read by `technical-design`, say — an explicitly empty section carries information, and those skills say so themselves. Delete the heading only when nothing downstream is waiting on it.

Where the source is incomplete, flag the gap with `#TODO` rather than filling it with plausible invention.

---

## Step 3 — Check before finishing

- [ ] Shape matches the content, not just what was asked for
- [ ] Every section from the asset is either filled or deleted — no empty headings, no leftover placeholder text
- [ ] No italicised authoring guidance left in the note
- [ ] Voice matches the shape and is consistent throughout
- [ ] Frontmatter carries topic-relevant `tags`; no `created` or `modified` added
- [ ] Links are specific and descriptive; unresolved links are deliberate
- [ ] Related Topics holds only notes not already linked in the body, or is deleted
- [ ] No trace of the source that produced the note — no origin narration, no borrowed emphasis

`references/structure-doctrine.md` carries the fuller checklist for structure and linking.

---

## Being called by other skills

This skill owns shape and style. Skills that write notes should call it rather than restating its content — if a convention appears both here and in another skill, this file wins and the copy is a bug.

A calling skill supplies the task and the source material. It should:

- Let this skill classify, unless the shape is already settled
- Read the asset and `references/obsidian-style.md` before writing anything
- Keep its own workflow concerns — approval gates, search-before-creating, transcript handling, file editing — and delegate everything about shape and notation here

---

## Reference Files

| File | Contents | Portable? |
|---|---|---|
| `references/choosing-a-format.md` | Full criteria per shape, confused pairs, worked examples | Yes |
| `references/structure-doctrine.md` | Split criteria, content density, linking, voice, source independence, quality checklist | Yes |
| `references/obsidian-style.md` | Obsidian notation, plus substitutions for non-Obsidian targets | No — Obsidian-specific |
| `assets/*.md` | One skeleton per shape | Obsidian-flavoured; shape is portable |
