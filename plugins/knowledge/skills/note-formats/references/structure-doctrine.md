# Structure Doctrine

How notes get split, how dense they should be, and how they connect. This file is tool-agnostic — it describes rules, not notation. For the syntax that expresses these rules in Obsidian, see `obsidian-style.md`.

---

## Deciding What Becomes Its Own Note

Split a concept into its own note when it:

- Can be understood and studied in isolation
- Is likely to be referenced across multiple topics
- Has enough substance to warrant more than two sentences

Do not split:

- Trivial definitions that fit in a bullet
- Concepts that only ever appear in one context
- Closely coupled ideas that lose meaning when separated

**If a title needs "and", split it.**

When a note grows long, split it into a parent covering the main subject and children covering the features or subtopics. Keep all of them concise — splitting is not an excuse for the parent to stay bloated.

**Split when concerns diverge.** If a note covers two distinct audiences or layers — server-side versus client-side, design versus implementation — split it. If the resulting concepts benefit from comparison or a shared introduction, add an overview or hub note above them.

---

## Content Density

### Concept notes

Include key concepts and correct terminology — enough for understanding, and enough that an AI could expand the note later from what is there. Avoid long essays; if a section grows, split it or link out.

When content is extracted into a child note, replace it in the parent with a **short summary placeholder** plus a pointer to the child. Do not leave a bare heading, and do not leave the full content behind:

```markdown
## S3 Bucket Keys

Server-side encryption key used per bucket to reduce KMS request traffic;
lowers cost and improves throughput.

See: <pointer to the S3 Bucket Keys note>
```

**Too sparse — avoid:**

```markdown
- Arrays
- Linked Lists
- Hash Tables
```

No attributes or relationships. Useless for recall and useless as a seed for expansion.

**Correct balance:**

```markdown
- **Arrays**: contiguous memory storage; O(1) index access; insertion/deletion
  costly (O(n) shift); foundation for dynamic arrays and most sequence structures
```

**Too verbose — avoid:** long paragraphs that either belong in a separate note, or that an AI could reconstruct from a shorter seed.

### Navigational notes

A heading, an optional one-sentence section description, and a list of links. Frame entries as wayfinding, not study content. If explanatory prose is accumulating, it belongs in a concept note instead.

---

## Linking Strategy

### Link to expand, not to decorate

Notes link to other notes that expand on a topic. Depth comes from linking, not from writing everything in one place. A note may be linked from one other note, from many, or from none.

Parent notes link to their children.

### Do not add reciprocal links

Obsidian's backlinks pane automatically surfaces every note that links to the current note. Adding the reverse link by hand duplicates that for no benefit. Only add a link that provides navigation value the backlinks pane does not already give you.

### Related Topics

A closing section for notes that are conceptually related but **not already linked in the body**.

- Keep it short — one or two links, sometimes none
- Omit the section entirely if there are no qualifying links
- Do not include: notes already linked inline, parent or index backlinks, or exhaustive lists of tangentially related material

### Layer discipline

Objective reference notes should link only to other reference notes. They carry no personal or project context, so if a link to a personal or project note seems necessary, that link belongs in the personal note pointing inward — not in the reference note pointing out.

---

## Voice

| Content | Voice |
|---|---|
| Factual, for learning and building mental models | Second person ("you") |
| Personal — opinions, preferences, how-I-do-it, reminders | First person ("I") |
| Procedural — steps to be executed | Imperative ("run", "confirm") |

Second person keeps reference notes general and reusable; they make sense to any reader, including future-you. First person signals that a note is personal and opinionated.

**The voice you naturally reach for is a signal.** If "I" feels right, it is a personal note. If "you" feels right, it is a reference note. Where a note's shape is ambiguous, voice is often the fastest tiebreaker.

Prefer one primary voice per note.

---

## Duplication

Prefer links over duplication. If the same fact would need updating in two places, it belongs in one note with pointers to it.

The *procedure* for searching before you create — and the judgement about whether to improve an existing note rather than write a parallel one — is workflow, not structure. It belongs to the calling skill; see `obsidian-note-writer` Step 2.

---

## Quality Checklist

- [ ] One concept per note; no title requires "and"
- [ ] Titles make sense out of context
- [ ] Content is dense enough for recall and for AI expansion; no filler
- [ ] Extracted content left a summary and a pointer behind, not a bare heading
- [ ] Navigational notes contain no explanatory content
- [ ] Links point to notes that genuinely expand the topic
- [ ] No reciprocal or duplicate links — the backlinks pane handles those
- [ ] Related Topics holds only notes not already linked in the body; omitted if empty
- [ ] Voice is consistent and matches the shape
