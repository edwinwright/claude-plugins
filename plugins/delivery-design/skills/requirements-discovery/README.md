# requirements-discovery

Works out what the problem actually is, who has it, and what already exists — before anyone commits to a solution.

Discovery is **elicitation**, not collection. Requirements are rarely sitting somewhere waiting to be written down: they are latent, contradictory, and held by people who describe solutions when asked about problems. This skill draws them out, then challenges the framing it drew out.

## When to use

At the start of client work, before `product-definition`. Also worth running standalone whenever a request arrives with a solution already attached and no agreed problem behind it.

## Routes

Runs at the head of the **Full** route.

Routes, escalation gates, and the work order format are defined in [`request-triage/references/routes.md`](../request-triage/references/routes.md).

## Inputs

| Input | Required | Notes |
|---|---|---|
| A brief, conversation, or complaint | Yes | Any form. A one-line frustration is enough to start. |
| Access to an existing system | No | Improves the current-state assessment considerably |
| Answers to up to 7 questions | Usually | Asked only for what could not be read from the input |

## Output

Three durable documents:

```
problem-statement.md   ← the problem, who has it, evidence, cost of doing nothing
stakeholders.md        ← who is affected, decides, or can block; interest and influence
current-state.md       ← systems, process, workarounds, volumes, pain points
```

They are durable rather than transient: they stay true after the work ships, and they answer "why did we build this" long after the requirements document has been archived.

### Where they go

**Asked once, then recorded.** These are the one output set in this pipeline that often should not live in the code repository — a stakeholder map carries names, org structure, and commercial context, and a repo can be handed over, forked, or opened up later.

The skill checks the host project's `AGENTS.md` or `CLAUDE.md` first. If nothing is configured, it offers a directory outside the repo (recommended for client work), a notes vault, or `docs/discovery/` in-repo, then records the choice so the question is asked once per project. If the target is inside the repo it flags that once and continues — it will not refuse a location you choose.

## How it works

```
Business Analyst  +  Challenge Lens     ← run in parallel
        ↓                    ↓
   framing, map,      revised framing,
   current state,     evidence quality,
   elicitation plan   missing voices
        ↓                    ↓
   Main agent reconciles → confirms with you → writes three files
```

1. **Business Analyst** — BABOK-framed elicitation: problem statement, stakeholder map, current-state assessment, and a plan of what to do next to close the gaps, by technique
2. **Challenge Lens** — attacks the framing: is the stated problem the real one, whose problem is it, what is the evidence actually worth, what happens if nothing is done
3. **Main agent** — reconciles the two, surfaces the disagreement rather than averaging it away, confirms the problem statement, then writes

The two lenses run **in parallel on the same raw input**, not in sequence. If the Challenge Lens saw the analysis first it would critique the analysis instead of the problem.

## Usage guidelines

| Agent | Model | Effort | Reason |
|---|---|---|---|
| Business Analyst | claude-sonnet-4-6 | Medium | Structured elicitation; the shape is well defined |
| Challenge Lens | claude-sonnet-4-6 | Medium | Contrarian reading of the same input |

Three things this skill deliberately will not do:

- **Propose solutions.** A solution already chosen is recorded as a constraint on the situation, not as an output.
- **Fill gaps with plausible guesses.** "Not established — nobody has asked the support team" is worth more than an invented number, because it is actionable and a fabricated volume gets designed against.
- **Average the two lenses.** Where they disagree about what the problem is, that disagreement is the finding.

**Next step:** Run `product-definition` to turn this into a vision, a non-functional baseline, and a prioritised backlog. Then `domain-modelling` for the glossary.

## Files

| File | Purpose |
|---|---|
| `SKILL.md` | Orchestration instructions |
| `README.md` | This file |
| `problem-statement-template.md` | Output template for `problem-statement.md` |
| `stakeholder-map-template.md` | Output template for `stakeholders.md` |
| `current-state-template.md` | Output template for `current-state.md` |
| `prompts/business-analyst.md` | Business Analyst subagent instructions |
| `prompts/challenge-lens.md` | Challenge Lens subagent instructions |
