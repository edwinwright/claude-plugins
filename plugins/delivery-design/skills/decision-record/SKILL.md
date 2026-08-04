---
name: decision-record
description: 'Write a Decision Record (MADR-style) for a contested, costly-to-reverse decision. Use this skill when a decision is not obvious from the code and should outlast the PR that implements it. Supports three scopes: architecture, product, and process. Applies a significance gate before writing — if the decision does not clear it, the skill refuses and tells the user where the rationale belongs instead. Triggers: "write a decision record", "record this ADR", "document this decision", "why did we choose X".'
---

# decision-record

You are helping the user write a MADR-style Decision Record for a contested, costly-to-reverse decision.

This is a standalone skill — it can be run at any point in the delivery pipeline.

---

## Step 1: Read the input

Read everything the user has provided: the decision description, context, alternatives considered, and any relevant docs (PRD, tech spec, codebase context).

---

## Step 2: Apply the significance gate

Before doing anything else, argue yourself *out* of writing this record. A Decision Record is only warranted when **all three** of the following hold:

1. **It was contested.** Real alternatives existed with genuine tradeoffs. If there were no real alternatives, there is no decision to record — the rationale belongs in a code comment.
2. **It is expensive to reverse.** It touches a data model, a public API, a vendor commitment, a security model, or money. Cheap-to-reverse decisions don't warrant records.
3. **The "why" is not recoverable from the code.** A competent future reader — or agent — looking at the code would ask "why on earth did they do it this way?" If a code comment or PR description carries the rationale adequately, that is where it belongs.

If **any of the three tests fail**, stop and tell the user:

> "This decision doesn't clear the significance gate. Here's why: [explain which test failed]. The rationale belongs [in a code comment / in the PR description / in a brief note in the ticket]."

Only proceed if all three tests pass.

---

## Step 3: Determine the scope

Identify which scope this decision falls under. Ask the user if it is not obvious from the context.

- `architecture` — technical decisions: data model, API design, infrastructure, framework choice, security model
- `product` — business/product decisions: pricing, scope cuts, target-user calls, build-vs-buy
- `process` — how the team works: ceremonies, technical practices, team agreements, collaboration tools

**Note for `process` scope:** Process decisions rarely clear the "expensive to reverse" test — most process choices can be rolled back in a sprint. If the scope is `process`, re-apply the gate with extra scepticism.

---

## Step 4: Run the review-lens subagents

Based on scope, spawn one or both review-lens subagents. These personas challenge whether the gate truly clears and surface any gaps in the alternatives considered.

- `architecture` scope → spawn the **Technical Lens** subagent only (`prompts/technical-lens.md`)
- `product` scope → spawn the **Product Lens** subagent only (`prompts/product-lens.md`)
- `process` scope → spawn the **Technical Lens** subagent only
- If the decision crosses multiple axes → spawn **both lenses in parallel**

Read the relevant prompt file(s). Spawn each as an anonymous subagent via the Agent tool using the file contents as the system prompt. Do not use any globally registered agent.

Pass each subagent:
- The decision description and context
- The alternatives considered
- The output of Step 2 (your significance-gate reasoning)

Wait for the subagent(s) to complete. If a lens identifies that the gate does not in fact clear, surface this to the user and stop.

---

## Step 5: Determine the file number

Look at the existing files in `docs/decisions/<scope>/`. Find the **highest** four-digit prefix already in use and increment it by one. Use a four-digit zero-padded number (e.g. `0001`, `0002`).

Read the highest prefix rather than counting files — counting mis-numbers as soon as a record is deleted, or when a file without an `NNNN-` prefix (a README, say) lands in the folder. Ignore any file whose name does not start with four digits and a hyphen.

If `docs/decisions/<scope>/` does not exist or holds no numbered records, this will be `0001`.

---

## Step 6: Fill the template and write the record

Read the template at `decision-record-template.md`. Fill every section.

- `status:` starts as `proposed`. Change to `accepted` only if the decision is already final. Never set to `superseded` on a new record — use that only when superseding an existing record.
- `deciders:` — the user's name or team, if known; otherwise leave as a placeholder.
- Keep **Alternatives Considered** substantive — this is the section that proves the gate cleared.
- **Consequences** must include both positive and negative (honest trade-off accounting).

Write the completed record to:

```
docs/decisions/<scope>/NNNN-<slug>.md
```

Where `<slug>` is a short kebab-case description of the decision (e.g. `postgres-over-dynamodb`, `usage-based-billing`).

---

## Step 7: Present and advise

Present the written record to the user.

Tell the user:

> "Decision record written to `docs/decisions/<scope>/NNNN-<slug>.md`. If this decision is already final, update `status: accepted`. If it supersedes an older record, update the older record's `status: superseded` and `superseded_by:` field, then set this record's `supersedes:` field to the old filename.
>
> Link from the relevant code or PR with a comment: `// see docs/decisions/<scope>/NNNN-<slug>.md`"
