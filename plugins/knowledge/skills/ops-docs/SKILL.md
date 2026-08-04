---
name: ops-docs
description: Turn a chat session, transcript, uploaded file, or described process into operational documentation — a Checklist, Runbook, or Playbook. Use whenever the user wants a process written up ("turn this into a runbook", "document these steps", "write this up as a procedure", "what's our decision tree for X", "I want to remember how to do this next time"), even when they don't name the document type. Applies equally to SDLC work and personal-life processes such as renewing documents, setting up accounts, or IT support tasks. Handles extracting structure from messy sources and proposing the set of documents a session implies; the document shapes themselves come from the note-formats skill.
---

# Ops Docs

Turns unstructured input — a chat session, a transcript, an uploaded file, or a described process — into operational documentation.

This skill owns the **workflow**: mining a messy source for structure, working out how many documents it implies, and getting agreement before anything is written. The **shapes** — Checklist, Runbook, Playbook — belong to the `note-formats` skill, along with the criteria for telling them apart.

Applies to SDLC work and personal-life processes alike. Do not assume engineering context.

---

## Step 1 — Identify the input

Several modes, often combined:

- **Current conversation** — no file or transcript given; treat everything above the trigger message as source material
- **Pasted transcript** — a chat log pasted directly
- **Uploaded file** — read it fully first
- **Research or description** — the user describes a process from memory, or asks you to research one ("look up how to renew a UK passport and turn it into a runbook")

Read or gather the full source before classifying anything. Do not start drafting structure mid-read.

---

## Step 2 — Classify

Invoke `note-formats` and use its `references/choosing-a-format.md`. Do not classify from memory — getting this wrong produces the wrong shape no matter how well written the content is.

If the user named a type, use it, but sanity-check it. If they asked for a Runbook and the content clearly branches into substantially different procedures, say so before proceeding.

### Step 2a — When the input implies multiple documents

This is the step that makes this skill worth invoking, and it is mandatory whenever a Playbook is being created from scratch.

If the source branches into distinct paths and the user has only asked for "a playbook" or "document this process":

- Identify each distinct path from the source
- Propose them as candidate Runbooks **before** writing anything — name each, give a one-line description, and say which Playbook path it serves
- Ask which to draft now and which to leave as an unresolved link for later. A Playbook path can legitimately point at a Runbook that does not exist yet
- Do not silently write five Runbooks the user did not ask for

The same applies in reverse: a session covering three unrelated tasks is three Runbooks, not one Playbook. A Playbook only exists where there was a real triage step routing between them.

---

## Step 3 — Mine the source

Read the shape's asset from `note-formats` first, then map the source onto it. What to look for while reading:

- **Reasoning behind choices.** Moments where a choice was made and a reason given — "we did X because Y", a correction, a trade-off discussion. These belong in Rationale, not in the steps.
- **Confirmation steps.** Where the source says how someone checked something worked ("then I checked X and saw Y"), capture it as verification rather than letting it flatten into an action step.
- **What actually varies per run.** Only treat something as a context variable if the source shows it genuinely changing between executions. Most one-off processes have none.
- **Gaps.** If the source jumps from step 2 to step 5, flag it with `#TODO`. Do not fill the gap with assumed steps — an invented step in a runbook is worse than a missing one, because it will be followed.

---

## Step 4 — Propose before writing

Present the drafted document in chat first. For a Playbook with candidate Runbooks, present the Playbook draft alongside the proposed Runbook list — do not write Runbook files until the user confirms which they want.

Ask explicitly whether:

- The classification feels right — they may know context you do not
- Any extracted step or path is materially wrong, as opposed to merely phrased differently from how they would have put it

Do not write files until the user has reviewed and approved.

---

## Step 5 — Write

Follow the asset and `references/obsidian-style.md` from `note-formats`. Read the host vault's `CLAUDE.md` for placement and filename conventions.

Two things specific to operational docs:

- Title descriptively — `Runbook - New AWS Client Account`, never `Runbook - Template`
- Cross-link both ways: a Runbook written to serve a Playbook path links back to the Playbook, and the Playbook's path links forward to it
