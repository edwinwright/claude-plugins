# Choosing a Format

Full criteria for every shape, the pairs that get confused, and worked examples. Tool-agnostic — these distinctions hold in any vault, repo, or wiki. Read this when the shape is not obvious from the shape index in `SKILL.md`.

**Default to the lightest shape that fits.** Do not reach for a Playbook because a situation feels important, or an Overview Note because a topic feels big. Reach for them because the content genuinely branches, or genuinely spans subtopics.

---

## The first cut

Ask what the reader is doing when they open it.

- **Building understanding** — learning, orienting, comparing, deciding how to approach something. Knowledge shapes.
- **Getting something done** — a task is in front of them right now. Operational shapes.

A note that would be read in both modes is usually two notes.

---

## Knowledge shapes

| Question | Shape |
|---|---|
| Is it purely links, with no content of its own? | **Map of Content** |
| Is it one self-contained concept? | **Atomic Note** |
| Does it introduce a topic area and point at the detail? | **Overview Note** |
| Does it place a set of related notes side by side to contrast them? | **Hub Note** |
| Does it compare options for a recurring class of choice? | **Decision Guide** |
| Does it explain how something works, or how to approach a process? | **Guide Note** |

### Atomic Note

**Use when:** the content is a single idea, complete enough to be understood without reading anything else, and focused enough that it does not try to cover two things.

**Tell:** you could link it from an unrelated topic and it would still make sense.

**Example:** "HTTP Header", "Gradient Descent", "Idempotency".

### Overview Note

**Use when:** a topic area has several atomic notes under it and someone needs orienting before they can use them. It carries introductory content of its own, then links out.

**Tell:** you are answering "what is this area, and where does the detail live?"

**Example:** an "AWS S3" note covering the service, its core concepts, and common use cases, linking out to notes on versioning, object lock, and bucket keys.

**Technology Overview is a variant, not a separate shape.** It is an Overview Note applied to a single technology: a feature survey, descriptive rather than prescriptive, written to refresh knowledge of something you have used before rather than to teach it from scratch. Same introduce-and-link-out structure. Use the Overview Note asset.

### Hub Note

**Use when:** several related notes need to be compared, and the value is in the relationships between them rather than in any new content.

**Tell:** you are answering "which of these do I want, and how do they differ?" If you find yourself writing substantial original prose, it belongs in an Atomic or Overview Note instead.

**Example:** "Scaling Strategies", "REST vs RPC vs GraphQL".

### Decision Guide

**Use when:** the same category of choice keeps coming up, and you want the comparison ready before you next face it.

**Tell:** the core deliverable is a "when to use each" table. It states the simplest default and the concrete trigger that justifies escalating to something more complex — that is what stops it becoming a feature list.

**Example:** "How do I manage local state in React", "Which AWS compute service for this workload".

### Guide Note

**Use when:** you are explaining how something works or how to approach a process, educationally, using placeholders rather than concrete values.

**Tell:** it is not triggered by an event. You read it to build understanding, then apply that understanding to your own situation. A Guide answers *how does this work and how do I approach it*; a Runbook answers *what do I do right now*.

**Example:** "How TLS certificate validation works", "How to approach a database migration".

### Map of Content

**Use when:** the deliverable is navigation — an index of a domain, a curriculum for a subject, a dashboard for an area of responsibility.

**Tell:** no content of its own. Heading, optional one-sentence section description, list of links. If it explains anything, it has stopped being a Map of Content.

Three variants:

- **Domain** — broad entry point for a major area. Links to topic maps, area notes, and key concept notes.
- **Topic** — detailed knowledge map for one subject, ordered as a curriculum: foundational concepts before dependent ones. Missing notes are listed inline as italicised gap entries so the map shows what is absent, not only what exists.
- **Area Dashboard** — personal dashboard for a responsibility or interest. Links to personal notes, goals, and relevant reference notes.

---

## Operational shapes

| Question | Shape |
|---|---|
| Does it describe your preferred approach rather than execute anything? | **Standard Note** |
| Does it record one decision already made, with its consequences? | **Decision Record** |
| Flat items, no sequence, no decisions? | **Checklist** |
| One task, one deterministic path? | **Runbook** |
| Branches into substantially different procedures? | **Playbook** |

The middle three form a hierarchy of weight:

| | Checklist | Runbook | Playbook |
|---|---|---|---|
| Sequence matters? | No | Yes | Not applicable — decision-driven |
| Decisions involved? | No | No, deterministic | Yes, that is the point |
| Branches into other documents? | No | No | Yes, into Runbooks |
| Typical length | A handful of items | Several steps to around 20 | Several paths, each pointing onward |

### Checklist

**Use when:** a flat set of things to do or verify, in no meaningful order, with no decisions and no step that depends on another's outcome.

**Tell:** written as a Runbook, no step would have an expected result worth noting, and reordering the items would break nothing.

**Examples:** a pre-merge checklist — tests pass, lint clean, changelog updated, reviewer assigned. Things to pack for a work trip.

### Runbook

**Use when:** one specific, known task with a deterministic sequence. Do this, then this, then this, and you get one outcome.

**Tell:** exactly one path through it. All decisions were made at authoring time and baked into the steps — during execution you follow, you do not deliberate. If you find an "if X do A, if Y do B" branch inside it, that is either two Runbooks or the seed of a Playbook.

**Examples:** "Provision a new AWS account for a client" — fixed sequence, only the context variables change per run. "Set up an email client with a new mail account."

**Common misclassification:** a process with a few optional steps ("if using 2FA, also do X") is still a Runbook. Optional sub-steps inside an otherwise linear flow do not make it a Playbook. The threshold is that the *overall shape* of what you are doing depends on a situational judgement.

### Playbook

**Use when:** a category of situation where the first job is working out what you are dealing with, and the right response differs meaningfully depending on the answer.

**Tell:** you cannot write a single ordered list that covers it. You would have to write "if this happened do A, if that happened do B", where A and B are substantially different procedures rather than a fork in one procedure.

**Examples:** "Production incident response" — could be a database issue, a bad deploy, a third-party outage; each is its own Runbook, and the Playbook's job is triage plus who to tell. "I am locked out of an account" — forgotten password, hacked account, deactivated account, expired 2FA device.

**Common misclassification:** do not write a Playbook because a task is important or high-stakes. A single critical task with one correct procedure is still a Runbook — just one with a prominent warning. Importance does not imply branching.

### Standard Note

**Use when:** capturing your settled, opinionated approach to a domain — the preferences and conventions you have decided on for how you do a particular thing.

**Tell:** it is never executed. It is consulted when you write or revise a Runbook, and when you want to remember why your approach is what it is. First person and prescriptive.

**Example:** "AWS Account Organisation Standard" — preferred account structure, OU hierarchy, baseline configuration. The Runbook that provisions an account follows those decisions without revisiting them.

### Decision Record

**Use when:** a specific decision has been made and the reasoning will not survive in the code or the pull request.

**Tell:** it records one decision, once. Context, options considered, what was chosen, and the consequences. It is consulted when revisiting the design, and superseded rather than edited.

An Architecture Decision Record is the architecture-scoped variant; the same shape records product and process decisions.

**Two homes, two shapes — route by destination.** This asset is for a decision recorded in the vault: tags frontmatter, wikilinks, a Related Topics section, read alongside your other notes. A decision belonging to a *codebase* goes in that repo as `docs/decisions/<scope>/NNNN-<slug>.md`, with `supersedes`/`superseded_by` fields and a numbered sequence — use the `delivery-design:decision-record` skill, which owns that shape and applies a significance gate before writing anything. If the decision would be looked up by someone reading the code, it is a repo ADR, not a vault note.

---

## Commonly confused pairs

**Overview Note vs Hub Note.** An Overview *introduces* a topic area and points at where the detail lives. A Hub *contrasts* a specific set of notes. Orienting someone to a subject for the first time is an Overview; placing several notes side by side to show how they relate is a Hub. They can coexist — an Overview for a domain may link to Hubs comparing options within it.

**Overview Note vs Map of Content.** An Overview has introductory content of its own and is tied to a concept. A Map of Content has no content and is tied to a domain or area. If you deleted every link, an Overview would still say something; a Map of Content would be blank.

**Guide Note vs Overview Note.** A Guide is process-oriented — how does this work, how do I approach it. An Overview is topic-oriented — what is in this area, where is the detail.

**Decision Guide vs Decision Record.** A Decision Guide is general and written *before* you face a specific instance, so a framework is ready when you need it. A Decision Record is specific and written *after*, recording the one decision you actually made. Use a Decision Guide to think it through; write a Decision Record to capture what you chose.

**Decision Guide vs Playbook.** Both route you toward an answer, and this is the sharpest of these distinctions. A Decision Guide chooses between *design options* while you are planning or building; the output is a choice. A Playbook routes to an *operational procedure* when an event has fired; the output is which Runbook to run. Planning versus responding.

**Playbook vs Standard Note.** A Standard is declarative — it describes how you do things and informs how Runbooks get authored. A Playbook is operational — it is opened in the moment and executed. A Standard is never triaged from.

**Standard Note vs Guide Note.** A Guide explains how something works in general, objectively, for any reader. A Standard records how *you* do it, subjectively, for you. The Guide is second person; the Standard is first.

---

## Worked example: three Runbooks or one Playbook?

Source material: a session where someone debugged three different laptop issues in a week — cleaning personal data off a colleague's work laptop, recovering a locked laptop, setting up an email client.

These are **three separate Runbooks**. Each is a distinct deterministic task with its own steps and outcome.

They are three paths of one Playbook only if a genuine triage step precedes them — "someone hands me a laptop with a problem, what do I check first to know which of these applies?" If that step exists in the source, *that* becomes the Playbook and these three become its named paths. If it does not exist, because the person already knew which task each laptop needed, there is no Playbook here at all.

This is the single most common classification error: treating "I did three related things this week" as automatically Playbook-shaped. It is only Playbook-shaped if there was a decision point routing between them.

## Worked example: one Atomic Note or several?

Source material: research into how DNS resolution works, covering record types, the resolver chain, caching, and TTLs.

- "DNS Record" types, the resolver chain, and caching behaviour each pass the split test — understandable in isolation, referenced from multiple topics, more than two sentences of substance. **Three Atomic Notes.**
- TTL is a property of a record, understood only in that context, and fits in a bullet. **Not a note** — it lives inside the DNS Record note.
- Something has to orient a reader arriving at the topic cold. **One Overview Note**, "Domain Name System (DNS)", introducing the area and linking out to the three.
- If the vault already has notes on several networking topics, they need an entry point. **A Topic Map of Content**, not another Overview.

The mistake to avoid here is the reverse of the Playbook one: creating an Overview Note plus a Map of Content plus four atomics for what is genuinely two notes' worth of content.
