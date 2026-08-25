---
name: domain-modelling
description: 'Bootstrap or update the project glossary and domain model. Use this skill when starting a new product (after product-definition), when onboarding onto an existing codebase, or when a new feature introduces terms and entities that should be formalised. Outputs docs/product/glossary.md and docs/product/domain-model.md. Triggers: "write the glossary", "document the domain model", "bootstrap domain docs", "update the glossary with these new terms".'
---

# domain-modelling

You are helping the user establish or maintain the project's shared vocabulary and domain model.

This skill produces two files:
- `docs/product/glossary.md` — the project's ubiquitous language
- `docs/product/domain-model.md` — entities, relationships, and invariants

These files are the highest-leverage agent-context docs in the repository. They stop implementation agents from inventing the wrong meaning for a term.

This skill is standalone — it can run at any point:
- **After `product-definition`** to bootstrap a new product's domain docs
- **After `backlog-refinement`** when a new feature introduces new domain concepts
- **Cold on an existing codebase** to document a legacy domain

---

## Step 1: Detect the current state

Check whether `docs/product/glossary.md` and `docs/product/domain-model.md` already exist.

- **Neither exists** → bootstrap mode (go to Step 2)
- **Both exist** → append mode (go to Step 3)
- **One exists** → bootstrap the missing one, then treat both as existing for future runs

---

## Step 2: Bootstrap mode

### 2a: Question mode

Ask targeted questions to elicit the core domain. Cap at 7 questions. Present them as a numbered list so the user can answer all at once.

Draw from these as needed:

1. What is this product or system? (One sentence.)
2. What are the 5–10 most important things this system manages or acts on? (These are your candidate entities.)
3. What are the key actions or verbs? (What do users, the system, or external actors *do* to those things?)
4. Are there any invariants — rules that must always be true? (e.g. "An order can only be cancelled before it ships.")
5. Are there any terms that have a specific meaning in this domain that differs from everyday usage?
6. Are there any terms you've seen used inconsistently across the codebase, tickets, or team conversations?
7. Are there any existing documents (README, onboarding notes, API docs) I should read before writing?

If the user shares existing documents (READMEs, API docs, codebase context), read them before proceeding. Identify candidate terms that appear frequently or are central to the domain.

### 2b: Write the initial files

Read `glossary-template.md` and `domain-model-template.md`. Write both files.

**For the glossary:**
- Include every term that has a non-obvious or context-specific meaning
- Do not include terms whose meaning is universal and unambiguous (e.g. "user", "email")
- Every entry should have a Definition that someone new to the domain could act on

**For the domain model:**
- Focus on entities that the system stores, manages, or acts on
- List their key attributes (not exhaustive — just what matters for understanding the domain)
- Show the relationships between entities clearly
- Capture invariants explicitly — they are the rules that must never be violated

---

## Step 3: Append mode

### 3a: Read the existing files

Read both `docs/product/glossary.md` and `docs/product/domain-model.md` in full.

### 3b: Identify gaps from the input

Read everything the user has provided — a new PRD, tech spec, decision record, or description of the new feature. Identify:

- New terms that appear but are not in the glossary
- Existing terms that are being used in a new or extended way
- New entities or relationships not captured in the domain model
- New invariants introduced by the feature

### 3c: Propose additions for confirmation

Present the proposed additions to the user:

> "Here are the changes I'd like to make to the domain docs:
>
> **Glossary additions:** [list each new term with proposed definition]
> **Domain model additions:** [list new entities / relationships / invariants]
>
> Any corrections or omissions before I write?"

Wait for confirmation before writing.

### 3d: Edit in place

Apply the confirmed additions to both files. Do not rewrite sections that are not changing. Do not delete existing entries.

---

## Step 4: Present and advise

Present the written or updated files to the user.

Tell the user:

> "Glossary and domain model are updated at `docs/product/`. These files are read automatically by the feature pipeline — `technical-design` and `backlog-refinement` will use them as context when processing new features.
>
> When you introduce new domain concepts in future features, run `domain-modelling` again to keep these files current."
