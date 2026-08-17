---
name: session-synthesis
description: Synthesise one or more chat transcripts, or the current conversation, into structured understanding and then optionally into notes. Use when the user pastes a transcript or attaches transcript files, says "synthesise this session", "what did I learn?", or asks to turn a conversation, chat log, or AI session into notes — including when they name only the destination ("integrate these into my vault", "write these up as notes") rather than the process, and when several related transcripts are handed over together.
---

# Session Synthesis

Takes a chat transcript (or the current conversation) and produces a synthesised conceptual map — not a linear summary. Content is reorganised by topic, the bigger picture is surfaced, and the result is something you can read to confirm or challenge your understanding.

This is distinct from note-taking. Synthesis comes first. Notes come later, optionally, when you decide you have understood something well enough to record it.

---

## Step 1 — Identify the Input

Two modes:

**Transcript provided:** the user has pasted a raw chat transcript into the message. Use that as the source.

**No transcript:** operate on the current conversation. Treat everything above the trigger message as the transcript.

Read the full source before doing anything.

---

## Phase 1 — Synthesis

### Step 2 — Read and Map

While reading, look for:

- What the user was actually trying to understand, not just the explicit questions asked
- Concepts that recurred or built on each other across multiple exchanges
- Points where understanding shifted or was corrected
- Moments where the user reframed something more precisely than the AI did — adopt their framing, not the AI's
- Corrections the user made mid-conversation; these are refinements, not noise

Then identify before writing:

- The distinct topics and how they relate
- The correct *logical* order — dependency order, not conversation order
- Which concepts are foundational and which build on them
- What the questions were collectively building toward

Distinguish core content from tangents. Do not structure the synthesis around tangents.

### Step 3 — Write the Synthesis

Present the synthesised understanding as a conceptual map with clear headings. Open with a brief statement of what the session was really about.

**Do:**

- Use tables and comparisons where clearer than prose
- Distinguish foundational concepts from their applications
- Note open questions or areas flagged for further exploration
- Adopt the user's framing where they expressed something better than the AI did

**Do not:**

- Walk through the chat in chronological order
- Format the output as Q&A
- Repeat every point — synthesise to the essential
- Preserve the AI's initial framing if the user later corrected it

### Step 4 — Checkpoint

After presenting the synthesis, stop. Do not proceed to notes.

Invite review: the user may want to push back, ask follow-up questions, or continue the conversation to fill gaps. Only move to Phase 2 when they explicitly say they are ready. Stopping at the synthesis is a valid outcome — notes are not always the goal.

---

## Phase 2 — Notes (optional, user-initiated)

Only proceed when the user explicitly says they are ready to make notes.

**The synthesis does not become the notes.** Everything about Phase 1 — its ordering, its emphasis, the fact that it opens by saying what the session was about — exists to reflect the conversation back to the user. None of that survives here. A note is organised by its subject and carries no trace of the session that produced it; re-derive each note's scope and emphasis from the subject rather than inheriting them from the synthesis in front of you. See *Source Independence* in `note-formats` → `references/structure-doctrine.md`.

### Step 5 — Propose Notes

Read the host vault's `CLAUDE.md` before proposing anything — it determines where each note belongs.

Propose:

- Which notes to create, and what shape each should take. Invoke `note-formats` to classify — a synthesis usually yields one hub or overview note carrying the connective understanding, plus atomic notes for the individual concepts
- Where each belongs, per the vault's placement rules
- How they relate — which notes link to which
- Whether any existing note should be updated rather than a new one created

Present proposals for approval. Do not write files until approved.

### Step 6 — Write Notes

Follow `note-formats` for shape and notation, and `obsidian-note-writer` for the write and refactor workflow. Update any index or home note that should reference the new notes.
