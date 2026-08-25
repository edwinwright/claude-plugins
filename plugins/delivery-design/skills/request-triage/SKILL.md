---
name: request-triage
description: 'Triage a raw feature idea or request before writing a PRD. Use this skill when the user has a rough idea and needs to decide whether to scope it fully (backlog-refinement) or park it in the backlog. The Product Owner lens lives here. Triggers: "I have an idea for", "new feature request", "should we build X", "add this to the backlog", "is this worth building", or any time the user wants to assess a request before committing to a PRD.'
---

# request-triage

You are helping the user triage a raw feature request — an idea, an ask from a user, a Slack message, a rough note. Your job is to assess whether it warrants a full PRD right now, should be parked in the backlog for later, or should not be built at all.

This is the front door to the feature pipeline:

```
request-triage → backlog-refinement → technical-design → delivery-planning → work-breakdown
```

---

## Step 1: Read the input and identify the product

Accept the request in any form — a one-liner, a quote from a user, a rough note, a Slack message. Do not ask for more detail yet.

Look for an associated product. Check for `docs/product/vision.md` and `docs/product/product-backlog.md`.

- If product context files are found, read them — you will pass excerpts to the product-lens subagent.
- If no product context exists and it is not obvious from the conversation which product this is for, ask: *"Which product is this request for?"* and wait for an answer before continuing.

---

## Step 2: Check for duplicates

Before running triage, scan for existing work that overlaps with this request:

1. Scan `docs/product/product-backlog.md` for entries with similar titles or goals.
2. List the directories in `docs/features/` — scan the feature names for overlap.

If you find a likely duplicate, surface it to the user before continuing:

> "This looks similar to an existing backlog entry: [Title]. Is this the same request, a refinement of it, or something distinct?"

Wait for confirmation if needed. If it is a duplicate, stop — do not add it again.

---

## Step 3: Run the product-lens subagent

Read `prompts/product-lens.md`. Spawn it as an anonymous subagent via the Agent tool using the file contents as the system prompt. Do not use any globally registered agent.

Model: `claude-sonnet-4-6`, effort: medium.

Pass the subagent:
- The raw request (verbatim)
- The product vision excerpt from `docs/product/vision.md` (if found; omit the section if not)
- The current backlog excerpt from `docs/product/product-backlog.md` (if found; include only the titles and user value columns — omit scope notes to keep it short; omit if not found)

Wait for the subagent to return a structured verdict before proceeding.

---

## Step 4: Handle "Needs clarification"

If the lens verdict is `Needs clarification`, surface the lens's clarifying questions to the user verbatim. Do not park, reject, or hand off to backlog-refinement until you have answers. Once the user provides answers, re-run Step 3 with the enriched request.

---

## Step 5: Act on the verdict

### Verdict: Brief now

Present this to the user:

> **Verdict: Ready to scope**
>
> [The lens's REASONING — 2–4 sentences]
>
> **Suggested summary for backlog-refinement:**
> [Write a one-paragraph summary of the request in plain language — what the user wants to do and why. This is the input they should pass to backlog-refinement.]
>
> Run `/backlog-refinement` with the summary above to write the PRD.

Do not run backlog-refinement automatically.

---

### Verdict: Park

Fill in `backlog-entry-template.md` using the lens's `BACKLOG TITLE` and `BACKLOG USER VALUE` fields. Add a `Scope Note` only if the lens's REASONING contains a deferral reason worth capturing (e.g. "blocked on X", "revisit after Y").

Append the new row to the appropriate MoSCoW section in `docs/product/product-backlog.md`. The MoSCoW section to use is the one matching the lens's `MOSCOW` field.

If `docs/product/product-backlog.md` does not exist, create it using the backlog structure from `product-definition/product-backlog-template.md` (or the standard MoSCoW table format) and add the entry.

Tell the user:

> **Verdict: Parked in backlog**
>
> [The lens's REASONING]
>
> Added to `docs/product/product-backlog.md` under **[MoSCoW section]**:
> — [Backlog title]: [User value]

---

### Verdict: Won't build

Present this to the user:

> **Verdict: Won't build**
>
> [The lens's REASONING]

Do not write anything to disk.

---

### Verdict: Needs clarification

(Handled in Step 4 — re-run after the user provides answers.)
