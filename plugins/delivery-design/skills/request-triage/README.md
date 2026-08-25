# request-triage

Triages a raw feature idea or request and routes it to the right place — a full PRD, the backlog, or nowhere.

## When to use

Use this before `backlog-refinement` whenever you have a rough request that has not yet been assessed. It is step zero of the feature pipeline:

```
request-triage → backlog-refinement → technical-design → delivery-planning → work-breakdown
```

Invoke it with phrases like:
- "I have an idea for..."
- "A user asked for..."
- "Should we build X?"
- "Add this to the backlog"
- "Is this worth doing?"

## Inputs

| Input | Required | Notes |
|---|---|---|
| Raw request | Yes | Any form — one-liner, user quote, Slack message, rough note |
| Product context | No | Reads `docs/product/vision.md` and `docs/product/product-backlog.md` automatically if present |

## Output

One of three outcomes:

| Outcome | What happens |
|---|---|
| **Brief now** | A one-paragraph summary of the request, ready to pass to `backlog-refinement` |
| **Park** | A new row appended to the appropriate MoSCoW section in `docs/product/product-backlog.md` |
| **Won't build** | The lens's reasoning presented to the user; nothing written to disk |

## How it works

1. Reads the raw request and finds the product context (vision, existing backlog)
2. Scans the existing backlog and feature directories for duplicates
3. Runs the **Product Lens** subagent — a senior Product Owner who assesses the request and returns a structured triage verdict
4. If the verdict is "Needs clarification", surfaces the subagent's questions to the user and re-runs triage with the enriched request
5. Acts on the verdict: hands off to backlog-refinement, appends to the backlog, or rejects with reasoning

## Usage guidelines

| Setting | Recommendation |
|---|---|
| Model | **claude-sonnet-4-6** is sufficient. The triage task is lightweight. |
| Effort | **Low to medium.** The subagent does the substantive assessment; the main agent routes and acts. |

The more product context you have on disk (`vision.md`, a populated backlog), the better the triage quality. Without a vision file, the product-lens subagent cannot assess alignment and will rely on the request alone.

## Pipeline

```
request-triage   →   backlog-refinement   →   technical-design   →   delivery-planning   →   work-breakdown
idea → verdict        PRD                 Tech Spec             Delivery Plan       tickets
```

**Next step:** If the verdict is "Brief now", pass the generated summary to `backlog-refinement`.

## Files

| File | Purpose |
|---|---|
| `SKILL.md` | Skill instructions |
| `README.md` | This file |
| `backlog-entry-template.md` | Template for a single MoSCoW backlog row |
| `prompts/product-lens.md` | Product Owner triage persona (subagent) |
