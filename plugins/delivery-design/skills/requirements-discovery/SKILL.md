---
name: requirements-discovery
description: 'Elicit requirements and frame the problem before a product or a large piece of work is defined. Covers stakeholder mapping, current-state assessment, and structured elicitation — interviews, observation, document analysis — producing a problem statement and the raw material that product definition turns into a vision and a backlog. Use at the start of client work, or whenever the problem itself is not yet agreed. Triggers: "requirements elicitation", "run a discovery", "who are the stakeholders", "what problem are we actually solving", "assess the current state", "discovery phase".'
---

# requirements-discovery

You are running a discovery: working out what the problem actually is, who has it, and what already exists, before anyone commits to a solution.

Discovery is elicitation, not collection. Requirements are rarely sitting somewhere waiting to be written down — they are latent, contradictory, and held by people who describe solutions when asked about problems. Your job is to draw them out and then challenge the framing you drew out.

## Routes

Runs at the head of the **Full** route, ahead of `product-definition`. Routes are defined in `../request-triage/references/routes.md`.

It is also worth running standalone whenever a request arrives with a solution already attached and no agreed problem behind it.

---

## Step 1: Read the input

Read everything provided — the brief, the conversation, prior notes, an existing system, a complaint. Identify what is asserted as fact, what is assumed, and what is a solution wearing a problem's clothing.

Do not ask questions yet. Most of what you would ask is usually answerable from what you already have, and asking anyway spends the user's patience on things you could have read.

---

## Step 2: Establish where the output goes

**Do this before writing anything.** Discovery outputs are the one document set in this pipeline that often should not live in the code repository: a stakeholder map and current-state assessment carry names, org structure, and commercial context, and a repository can be handed over, forked, or opened up later.

1. **Check the host project's `AGENTS.md` or `CLAUDE.md`** for a configured discovery location. If one is set, use it and skip the rest of this step.
2. **Otherwise, ask once:**

   > **Where should the discovery documents go?**
   >
   > These will include stakeholder names, organisational context, and an assessment of the current system. That is usually fine in a private repo and rarely fine in one that might be published or handed over.
   >
   > 1. A directory outside the code repository — recommended for client work
   > 2. A notes vault or documents folder
   > 3. `docs/discovery/` inside the repo — fine for your own products

3. **Record the answer** in the host's `AGENTS.md` (or `CLAUDE.md`) so this is asked once per project rather than once per run. One line is enough:

   ```
   - Discovery documents live in <path> (not in this repo).
   ```

4. **If the chosen location is inside the code repository,** say so plainly once and move on:

   > "Noted — these will live in the repo. Keep individuals' names out of them if this repository might ever be published or handed over; roles work just as well for everything downstream."

Do not refuse a location the user chooses. Flag the consideration, record the decision, continue.

---

## Step 3: Question mode

Ask only for what you could not determine from the input. Present the questions as a numbered list so they can be answered in one pass.

**Cap at 7 questions.** If you would need more than seven to frame the problem at all, say so and ask for a conversation or a document rather than running a long interrogation.

Draw from these as needed:

1. Who feels this problem most acutely day to day, and what do they do about it now?
2. Who else is affected, decides, funds, or can block this?
3. What does the current process or system look like, including the workarounds people have built?
4. What has already been tried, and why did it not stick?
5. What happens if nothing changes — is this expensive, or merely annoying?
6. How would you know, six months on, that this had been solved?
7. What is fixed and cannot be changed — budget, deadline, platform, regulation, an existing contract?

If the user names a solution rather than a problem, ask what would go wrong if they did not build it. That question does more work than any other here.

---

## Step 4: Run the two lenses in parallel

Read `prompts/business-analyst.md` and `prompts/challenge-lens.md`. Spawn both as anonymous subagents **in the same turn** via the Agent tool, using each file's contents as the system prompt. Do not use any globally registered agent — create fresh subagents with these exact instructions.

Configure each with:
- model: claude-sonnet-4-6
- effort: medium

Both receive the same inputs: everything the user provided, plus the answers from Step 3.

They run in parallel deliberately. The Challenge Lens must not see the Business Analyst's framing before forming its own, or it will critique the analysis instead of the problem.

Wait for both to complete.

---

## Step 5: Reconcile and confirm

The two lenses will disagree about what the problem is. That disagreement is the useful output — do not average it away.

Where they conflict, present both readings to the user and ask which matches their understanding:

> **The Business Analyst reads the problem as:** [framing]
> **The Challenge Lens argues it is actually:** [framing]
>
> [One or two sentences on what turns on the difference — what would be built differently under each.]
>
> Which is closer?

If the Challenge Lens found that the stated problem is a symptom, or that the person who has the problem is not the person asking for the solution, surface that before anything else. It is the most expensive thing to discover late.

**Confirm the problem statement before writing.** Everything downstream is built on it.

---

## Step 6: Write the documents

Write three files to the location established in Step 2, using the templates in this skill:

| File | Template | Contains |
|---|---|---|
| `problem-statement.md` | `problem-statement-template.md` | The problem, who has it, evidence, cost of doing nothing |
| `stakeholders.md` | `stakeholder-map-template.md` | Who is affected, decides, or can block; their interest and influence |
| `current-state.md` | `current-state-template.md` | Systems, processes, workarounds, and pain points as they exist today |

Rules for all three:

- **Separate what you were told from what you concluded.** Mark inference as inference. A discovery document that reads as uniformly certain is not usable — the reader cannot tell which parts to re-check.
- **Record gaps as gaps.** "Not established — nobody has asked the support team" is worth more than a plausible guess, because it is actionable.
- **No solutions.** If a solution has already been decided, note it under constraints as a fact about the situation, not as an outcome of discovery.

---

## Step 7: Present and hand off

Present the three files.

Tell the user:

> "Discovery is written to `[location]`. These are durable — they stay true after the work ships, and they answer 'why did we build this' long after the requirements document is gone.
>
> **Next:** run `product-definition` to turn this into a vision, a non-functional baseline, and a prioritised backlog. Then `domain-modelling` for the glossary.
>
> [If any gaps were recorded:] Before that, these are still open: [list]. They will limit how much of the backlog can be prioritised with confidence."
