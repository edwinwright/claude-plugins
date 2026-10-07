---
name: request-triage
description: 'Triage an incoming request — a feature idea, a bug report, a change ask, a new product — and choose its delivery route: direct (straight to work breakdown), standard (refine the requirements first), or full (discovery and design first). Every piece of delivery work starts here. Decides whether the request is worth building, checks the backlog for duplicates, and writes a work order with its ID and a Steps checklist naming which skills run, in order. Also reports where an existing work order has got to and what runs next. Triggers: "I have an idea for", "should we build X", "new feature request", "is this worth building", "add this to the backlog", "what route does this take", "where do I start", "what''s next on this work order".'
---

# request-triage

You are helping the user triage a raw request — an idea, a bug report, an ask from a user, a Slack message, a rough note.

You make two decisions, in this order:

1. **Should this be built at all?** Now, later, or never.
2. **Which route does it take?** Direct, Standard, or Full.

This is the front door to every piece of delivery work: everything starts here. Read `references/routes.md` before deciding anything. It defines the three routes, the escalation gates, the work order format, and the Steps checklist each route writes, and it is the only place those are defined.

---

## Step 0: Is there already a work order?

If the user points at an existing work order, or asks where a piece of work has got to ("what's next on MD-123", "where was I"), do not triage it again. Read `docs/work/<ID>-<slug>/work-order.md` and report:

> **[ID]: [Title]** ([route])
>
> [The Steps list as it stands, ticked and unticked]
>
> Next: `[first unticked step]`

Then stop. Re-triaging work that already has a route would overwrite a decision other skills have acted on.

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
2. List the directories in `docs/work/` and scan the slugs for overlap. Include `docs/work/_archive/`: work that already shipped is the most useful kind of duplicate to find, because the answer may be "this already exists".
3. If the project has work from an earlier layout, list those folders too. `references/artefacts.md` names them.

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

If the lens verdict is `Needs clarification`, surface the lens's clarifying questions to the user verbatim. Do not park, reject, or route the work until you have answers. Once the user provides answers, re-run Step 3 with the enriched request.

---

## Step 5: Act on the verdict

### Verdict: Build now

Select the route, then write the work order.

#### 5a: Select the route

Read `references/routes.md` if you have not already. Work through it in this order:

1. **Start at Standard.** It is the default, and it stays the default unless one of the next two tests moves you off it.
2. **Test for Direct.** Would a requirements document say anything the request does not already say? If you cannot name what it would add, take Direct.
3. **Test the five escalation gates.** Take Full only if you can name a gate that is *actually* true. Check `docs/domain/glossary.md` before claiming the domain-concepts gate; check `docs/product/nfr.md` before claiming the non-functional gate. A gate that is probably true but unconfirmed is not a gate — say so and stay on Standard.

The lens's `ROUTE` and `GATES` fields are an input to this decision, not the decision. You have read the glossary and the NFR baseline; the lens has not.

**A new product** (no `docs/product/vision.md` for it, or the request is a whole product rather than a feature) is the product bootstrap shape of Route C in `references/routes.md`. Record `escalation_gates: [new product]`.

#### 5b: Write the work order

Derive a slug from the request: lowercase, kebab-case, three words at most (`csv-export`, `stripe-subscriptions`, `pricing-page-typo`).

Find the work order's ID. Read **Work order IDs** in `references/artefacts.md` and follow it in order: a tracker issue the request arrived as, then a tracker issue created with the user's agreement, then the prefix declared in `AGENTS.md`, and only then ask. Never create a tracker issue without a yes.

Read `work-order-template.md` and write it to:

```
docs/work/<ID>-<slug>/work-order.md
```

Set `id`, `route`, and `escalation_gates`: the named gates, non-empty whenever `route: full`, empty otherwise.

Fill `## Steps` from the list for this route in `references/routes.md`, copied line for line. On Full, include the `domain-modelling` line only if the new-domain-concepts gate is named; on a product bootstrap, include `requirements-discovery` only if the problem is not yet agreed. Do not add `ticket-publish` or `decision-record` lines; the user adds those if they want them tracked.

**If this request is a backlog item of a product whose bootstrap work order is still open** in `docs/work/`, and every other line of that work order is ticked, close it: tick its last line, set its `status: done`, and `git mv` its folder to `docs/work/_archive/`. Say that you did.

Check the host project's `AGENTS.md` or `CLAUDE.md` for a different work directory before writing; `docs/work/` is the default, not a fixed path.

#### 5c: Hand off

Present this to the user:

> **Verdict: Build now — [Direct | Standard | Full] route**
>
> [The lens's REASONING — 2–4 sentences]
>
> **Why this route:** [One or two sentences. On Full, name each gate and what makes it true. On Direct, say what a requirements document would have added and why the answer is nothing.]
>
> Work order written to `docs/work/<ID>-<slug>/work-order.md`.
>
> [The Steps list]
>
> Next: `[first step in the list]`

Do not run the next skill automatically.

---

### Verdict: Park

Fill in `backlog-entry-template.md` using the lens's `BACKLOG TITLE` and `BACKLOG USER VALUE` fields. Add a `Scope Note` only if the lens's REASONING contains a deferral reason worth capturing (e.g. "blocked on X", "revisit after Y").

Append the new row to the appropriate MoSCoW section in `docs/product/product-backlog.md`. The MoSCoW section to use is the one matching the lens's `MOSCOW` field.

If `docs/product/product-backlog.md` does not exist, create it using the backlog structure from `../product-definition/product-backlog-template.md` (or the standard MoSCoW table format) and add the entry.

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
