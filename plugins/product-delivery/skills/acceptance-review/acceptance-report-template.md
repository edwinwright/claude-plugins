---
work_order: [YYYY-MM-slug]
reviewed: YYYY-MM-DD
outcome: accepted | accepted with waivers | not accepted
---

# Acceptance Report — [Work order title]

## Outcome

**[Accepted | Accepted with waivers | Not accepted]**

[One or two sentences. If not accepted, lead with what is outstanding.]

| | Count |
|---|---|
| Criteria verified | |
| Passed | |
| Failed | |
| Unverifiable | |
| Waived | |

---

## Verification

| # | Criterion | Verdict | Evidence |
|---|---|---|---|
| 1 | | PASS / FAIL / UNVERIFIABLE | |

### Commands run

```
[command]  → [result]
```

### Waivers

Only present if the gate was overridden. Delete this section otherwise.

| Criterion | Verdict | Waived by | Reason |
|---|---|---|---|
| | | | |

### Scope notes

Work built that no criterion covers, or criteria whose scope changed since they were written. Both usually mean a decision was made during the build — check it was harvested below.

- 

---

## Harvest

What was promoted out of the transient documents, and where it went.

### Glossary

| Term | Added or redefined | Landed in |
|---|---|---|
| | | |

### Domain model

| Type | Statement | Landed in |
|---|---|---|
| Entity / Relationship / Invariant | | |

### Non-functional baseline

| Requirement | Change | Landed in |
|---|---|---|
| | | |

### Decisions

| Decision | Record |
|---|---|
| | `docs/decisions/<scope>/NNNN-<slug>.md` |

### Considered and not promoted

Candidates that were raised and rejected, with the reason. Recorded so the same question does not get reopened at the next review.

| Candidate | Why not |
|---|---|
| | |

---

## Archive

- **Moved to:** `docs/work/_archive/[YYYY-MM-slug]/`
- **`AGENTS.md` entry removed:** yes / no
- **Recover with:** `git log --diff-filter=D -- docs/work/[YYYY-MM-slug]`

Nothing was deleted. Delete the archived folder by hand once you are satisfied nothing was lost:

```
rm -rf docs/work/_archive/[YYYY-MM-slug]
```
