---
name: obsidian-note-writer
description: Workflow for creating, enhancing, and refactoring notes in an Obsidian vault — searching for existing notes before creating new ones, improving notes without losing content, splitting and merging safely, and editing files. Use when the user asks for notes on a topic, wants existing notes cleaned up or restructured, or wants content moved between notes. Shape and formatting come from the note-formats skill; this skill covers the process around them.
---

# Obsidian Note Writer

The workflow for getting notes into and around a vault safely. It does not define what a note looks like — **shape, structure and notation come from the `note-formats` skill**, which is the source of truth for both. Call it rather than working from memory.

Read the host vault's `CLAUDE.md` before writing or moving anything. Folder structure, placement rules, and filename conventions are vault-specific and live there.

---

## Step 1 — Classify the task

| Ask | Task |
|---|---|
| Notes on a topic | **Create** — new notes from scratch |
| Improve or clean up existing notes | **Enhance** — read fully, preserve scope, improve in place |
| Split, merge, move, or rename | **Refactor** — see Step 4 before touching anything |

---

## Step 2 — Search before creating

Never create a note without checking whether one already exists.

1. Search by filename using topic keywords
2. Search note bodies for related links and headings
3. Search broadly — a flat reference folder means related notes may not surface on the first try
4. Check archived material; an archived note may hold content worth extracting first

If a note exists but is incomplete or outdated, **improve it rather than creating a parallel one**. Only create when the concept is genuinely absent.

On what to do when the same fact wants to live in two notes, see `note-formats/references/structure-doctrine.md` — it owns the duplication rule.

---

## Step 3 — Decide the shape

Invoke `note-formats`. Let it classify unless the user has already named a shape, then follow its asset and `references/obsidian-style.md` for the notation.

Where the material implies several notes, propose them before writing — name each, give a one-line description and its shape, and ask which to draft now. Do not write a pile of notes the user asked for one of.

---

## Step 4 — Enhancement and refactoring

### Enhancing an existing note

- Read and understand the whole note before changing anything
- Preserve the original scope — do not expand it unless there are critical gaps
- Correct factual errors; add brief clarifications (one or two sentences) for complex concepts
- Fill critical gaps; flag anything needing research with `#TODO Description`
- Remove duplicated information and group related concepts

### Refactoring safely

Moving, splitting, and merging is where content gets lost. These are not optional.

- **Migrate images.** Carry every embedded image into the new note at the right position. This applies to rewrites as much as to moves and splits — never drop an embedded image. Diagrams are content, not decoration.
- **Preserve all links.** Carry forward every wikilink from the source; do not silently drop references.
- **Write first, delete second.** Do not delete the source note until the replacement is written *and* the user has confirmed the deletion.
- **Fix inbound links** in other notes after a rename.
- **Leave a summary behind.** When a section is extracted into its own note, replace it with a short summary and a pointer, not a bare heading.

`note-formats` `references/structure-doctrine.md` covers when to split and how much content each note should carry.

---

## Step 5 — Editing files

Use `Edit` to replace a specific section. Reserve a full read-and-rewrite for changes too extensive to target.

When presenting a patch for the user to apply by hand, make it copy-pasteable and say where it goes:

> Here is the updated **Section Name** — paste this under the `### Section Name` heading in Obsidian:
>
> ```markdown
> [content here]
> ```

---

## Step 6 — Conciseness pass

- Remove redundant phrases and filler
- Every section should be scannable
- Clarity over brevity, but never pad

---

## Quality Checklist

- [ ] Searched for existing notes before creating anything new
- [ ] Shape came from `note-formats`, not from memory
- [ ] Host vault `CLAUDE.md` consulted for placement and naming
- [ ] Enhancement preserved the original scope; nothing essential removed
- [ ] `#TODO` items describe what actually needs research
- [ ] Images and wikilinks migrated intact through any move or split
- [ ] No source note deleted without written replacement and explicit confirmation
- [ ] Inbound links fixed after any rename
