---
name: coding-conventions
description: Personal coding conventions for TypeScript, React, and Next.js — the judgement calls a linter cannot make, plus the standard setup a new repository gets. Use when writing or refactoring TypeScript or JavaScript, creating React components, deciding where a file or folder belongs, reviewing code for anything beyond lint errors, or setting up a new project. Covers naming that carries meaning, function and module boundaries, error handling, server/client component choice, feature-based folder structure, and the lint and format stack.
---

# Coding Conventions

My settled preferences for how code should be written. Prescriptive, not educational — this is what I do, not a survey of what is possible.

## What this owns, and what it does not

This skill holds **only the rules a linter cannot enforce**. Anything a linter decides deterministically belongs in the lint configuration and nowhere else — duplicating it here means maintaining one rule in two systems, and the copy here is the one that goes stale.

| Belongs to the lint config | Belongs here |
|---|---|
| `no-explicit-any`, `prefer-const`, `no-var` | When `unknown` is the right type and when to model a union instead |
| `naming-convention`, `filename-case` | What a name should *say* |
| `import/order`, `no-empty` | Where a module boundary belongs |
| Anything with an autofix | Anything needing a judgement call |

If you find yourself about to write a rule here that ESLint already enforces, stop — the lint config owns it.

## Reading the host project first

These are defaults, not overrides. A project's own `CLAUDE.md` and its existing code both outrank this skill:

1. **Read the host project's `CLAUDE.md`** for its stack, structure, and constraints before applying anything here.
2. **Match the surrounding code.** Consistency within a file beats consistency with this skill. Where an established codebase does something differently, follow it and flag the divergence rather than silently converting.

This skill never dictates a folder tree for a project that already has one.

## References

Load only what the task needs.

| File | Read when |
|---|---|
| `references/typescript.md` | Writing or reviewing any TypeScript — naming, functions, types, error handling |
| `references/react-nextjs.md` | Building components, choosing server vs client, placing a file |
| `references/project-setup.md` | Starting a new repository, or configuring lint, format, and the lint hook |
