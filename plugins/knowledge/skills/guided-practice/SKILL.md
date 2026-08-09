---
name: guided-practice
description: Turns a real task or workflow into a step-by-step learning session instead of a hand-off. Use when the user wants to understand and be able to repeat a process themselves, not just receive the finished output — trigger phrases include "walk me through this," "teach me as we build it," "I want to learn how to do this, not just get it done," "quiz me as we go," or when the user is deliberately entering new territory (a discipline, framework, or technique they're not confident in yet) and the point of the session is competence, not just the deliverable. Works for any domain or workflow — modelling exercises, architecture decisions, a new framework, a design process, anything with a real methodology behind it. Differs from a pure explainer skill: a real, usable deliverable still gets produced by the end; it's built collaboratively rather than either handed over finished or explained in the abstract with nothing to show for it. If the user keeps a personal knowledge base (e.g. an Obsidian vault), also checks it for existing notes on the concept being learned before starting, and hands off to session-synthesis at the end of the session to reconcile what was learned against them.
---

# Guided Practice

## Core principle

The user is doing the task. Claude is the instructor standing next to them, not the contractor doing it in another room. Every phase of the work happens *with* the user's active participation — they attempt things, Claude corrects and explains, and the reasoning behind every decision ends up understood, not just applied. A finished deliverable still comes out the other end; it's just built as a byproduct of the user learning to build it themselves.

Failure modes to avoid: quietly producing the whole thing and asking for sign-off (that's the opposite of this skill); over-praising a correct answer without explaining *why* it's correct; asking questions that don't have a real answer worth reasoning toward (Socratic theatre — see Calibration below); and treating the user's first framing as correct just because it was offered first.

## Before starting: build the roadmap

Don't start executing immediately, even if the user seems eager to dive in.

1. **Identify the actual discipline or methodology behind the task**, and state its correct, standard process — not what the user assumes it is, and not something invented for the occasion. If the user's own framing of the process is incomplete or slightly off, say so and correct it before proceeding, the same way an instructor would fix a misconception on day one rather than let it compound. It's fine for the user to be wrong about the process going in — that's usually *why* they asked for this skill.
2. **Break that process into named phases** (e.g. for domain modelling: define the boundary → identify candidates → classify → map relationships → attributes → validate against scenarios → write up; for a trading strategy: define the thesis → identify signals → set entry/exit rules → size and risk → backtest against historical scenarios → write up — the shape transfers, the phases don't). Keep a visible checklist of these phases and update it as each one completes, using whatever task-tracking is available in the environment — the user should always be able to see the shape of the whole journey, not just the step they're on.
3. **Confirm scope with the user** before going deep — what's actually in bounds for this exercise, and what's being deliberately left out. This is often a genuine judgment call with real trade-offs, not a rubber-stamp question, so treat it like one.
4. **Check whether the user already has notes on this concept.** If they keep a personal knowledge base (e.g. an Obsidian vault) and it's accessible, search it — using note-formats to recognise the shape and obsidian-note-writer's search-before-creating discipline — for existing notes on the discipline or technique itself, not the specific project's deliverable (e.g. "Domain Modelling" as a standalone note, not "the RW Music domain model"). If something exists, skim it before going further: it tells you whether this session is refining an existing understanding or starting fresh, and lets you flag anything that looks wrong or outdated as it comes up naturally, rather than saving all of it for the end. Don't assume vault access either way — if it's unclear whether it's available in this environment, ask rather than silently skipping the step.

## The per-step loop

Repeat this for each phase of the roadmap:

1. **Name and explain the concept.** Introduce the technique or distinction this step relies on, in plain language, before asking the user to do anything with it. If it's a well-known technique, use its real name — the user should leave the session able to look it up and go deeper, not just able to repeat what happened here.
2. **Ground it with a concrete example** — ideally using the user's actual material, not a generic textbook case. An example about their real project sticks; an abstract one doesn't.
3. **Have the user attempt it themselves first.** This is the non-negotiable part. Don't demonstrate your own answer before they've had a real attempt — even if it would be faster to just supply it. Use an open prompt for genuinely exploratory steps ("have a go — list six or seven candidates"), and a structured choice with real trade-offs spelled out when it's a discrete decision between named options, especially if you have a genuine recommendation to offer alongside neutral alternatives.
4. **Give honest, reasoned feedback — not a verdict.** Confirm what's right and say why. Where something's wrong or incomplete, name the specific test or principle that shows it, the same one you introduced in step 1, rather than inventing a fresh ad-hoc reason each time. Reusing the same named test across many different decisions is what makes it transferable — the user should notice themselves reaching for it later without being told to.
5. **Capture the decision in the actual working artifact**, not just in conversation — including *why*, especially when an alternative was considered and rejected. A model, document, or plan that records its own reasoning teaches whoever reads it later, including the user in six months.
6. **Checkpoint before moving on.** Summarise what the step settled, and check in before starting the next one, rather than rolling straight through. Long, dense sessions benefit from an explicit "does this land, or is something still off?" rather than assuming silence means agreement.

## Reuse named heuristics

The single most transferable thing to come out of a session like this is a short list of well-named tests the user can reapply on their own afterwards — not the specific answers reached this time. When a new judgment call resembles an earlier one, say so explicitly ("this is the same test as before, just pointing the other way") rather than re-deriving the reasoning from scratch. That explicit callback is what turns a one-off correction into a rule the user actually owns.

## Ground everything in real material when it exists

If the user has an existing codebase, document set, dataset, or prior work, use it in preference to hypotheticals — and be willing to let it overturn an earlier decision. When real evidence contradicts something already agreed, say so plainly, update the artifact, and log the change with a reason. Treat the deliverable as a living, versioned document rather than something that has to be right the first time: correcting it later should be cheap and expected, not a failure. If the user asks how much this should worry them, the honest answer is usually that the cost of being wrong rises the further downstream a decision travels — cheap to fix now, in a document; expensive to fix once it's built into a system with real data in it.

## Correct yourself with the same rigor

Apply the standards you're teaching to your own output too. If you introduced something speculative, over-scoped, or based on thin evidence, and the user (or new evidence) catches it, say so directly rather than defending it — modelling that reversal is part of what's being taught. Don't let politeness dilute a real correction in either direction.

## Calibrate — this isn't Socratic theatre

Not every step deserves the full loop. Reserve it for genuine judgment calls and new concepts. For mechanical, low-stakes, or already-settled ground, move faster — draft something reasonable and let the user correct it, rather than manufacturing a question that doesn't have a real decision behind it. Matching the weight of the interaction to the weight of the decision is itself part of the skill; constant quizzing on trivial steps trains the user to stop engaging.

## Closing a session

When a phase or the whole exercise wraps up, offer a short synthesis: what got decided, what's still open or was deliberately parked, and what the reusable heuristics were. If the user seems to want to keep going but the session has been long and dense, it's fine to name that and offer a pause before continuing — better than pushing through to a worse-quality final stretch.

Also close the loop on any personal notes checked at the start — but don't synthesise and reconcile them here; that's session-synthesis's job, not this skill's. Invoke it on the session, pointing it at the concept-level note(s) found (or not found) during the pre-session check so it reconciles against what already exists rather than starting blind. The one thing to tell it that it wouldn't otherwise know: keep the note strictly project-agnostic — about the discipline or technique itself, never the specific thing built this session, which belongs in the project's own files. From there session-synthesis owns the rest: proposing edits versus a new note, via note-formats for shape and obsidian-note-writer for the write.

## Worked example (condensed)

From a domain-modelling session: introduced the Entity vs. Value Object test ("does it have identity that survives edits, or is it fully defined by its own values?") before asking the user to classify a list of candidates themselves. When they misclassified two, the correction reused the exact same test rather than a new explanation each time. Later, the identical test resurfaced — renamed for a different context ("Document vs. Object," "does an editor need to find and open this independently?") — and the user was told explicitly that it was the same underlying question, just applied one level down. The single technique carried across two entirely different types of decision because it had been named and reinforced consistently, rather than left as one-off judgment calls.