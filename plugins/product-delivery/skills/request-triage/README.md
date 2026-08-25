# request-triage

Triages a raw feature idea or request and routes it to the right place — a full PRD, the backlog, or nowhere.

## When to use

Use this on any incoming request that has not yet been assessed — a feature idea, a bug report, a change ask.

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
| **Build now** | A work order at `docs/work/YYYY-MM-<slug>/work-order.md` naming the route and, on Full, the gates that opened |
| **Park** | A new row appended to the appropriate MoSCoW section in `docs/product/product-backlog.md` |
| **Won't build** | The lens's reasoning presented to the user; nothing written to disk |

## How it works

1. Reads the raw request and finds the product context (vision, existing backlog)
2. Scans the existing backlog and feature directories for duplicates
3. Runs the **Product Lens** subagent — a senior Product Owner who assesses the request and returns a structured triage verdict
4. If the verdict is "Needs clarification", surfaces the subagent's questions to the user and re-runs triage with the enriched request
5. Acts on the verdict: selects a route and writes the work order, appends to the backlog, or rejects with reasoning

Route selection is the main agent's call, not the lens's. The lens recommends; the main agent has read the glossary and the non-functional baseline and makes the decision.

## Usage guidelines

| Setting | Recommendation |
|---|---|
| Model | **claude-sonnet-4-6** is sufficient. The triage task is lightweight. |
| Effort | **Low to medium.** The subagent does the substantive assessment; the main agent routes and acts. |

The more product context you have on disk (`vision.md`, a populated backlog), the better the triage quality. Without a vision file, the product-lens subagent cannot assess alignment and will rely on the request alone.

## Routes

The entry point on **every** route. This is the skill that chooses which route the work takes and records it in the work order.

Routes, escalation gates, and the work order format are defined in [`references/routes.md`](references/routes.md) — this skill owns them, and no other skill restates them.

**Next step:** Run whichever skill the route names — `work-breakdown` on Direct, `backlog-refinement` on Standard and Full.

## Files

| File | Purpose |
|---|---|
| `SKILL.md` | Skill instructions |
| `README.md` | This file |
| `references/routes.md` | The three delivery routes, the escalation gates, and the work order format — owned here, referenced everywhere else |
| `work-order-template.md` | Template for `docs/work/YYYY-MM-<slug>/work-order.md` |
| `backlog-entry-template.md` | Template for a single MoSCoW backlog row |
| `prompts/product-lens.md` | Product Owner triage persona (subagent) |
