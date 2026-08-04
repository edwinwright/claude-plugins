# Obsidian Style

**This file is Obsidian-specific.** It covers syntax, not shape — wikilinks, callouts, frontmatter, and the rest of the notation an Obsidian note is written in. The shapes themselves (in `assets/`) and the classification doctrine (in `choosing-a-format.md`) are portable; this file is not. If the output target is not Obsidian, see [Non-Obsidian targets](#non-obsidian-targets) at the end.

---

## Frontmatter

Every note opens with a YAML frontmatter block.

```yaml
---
tags:
  - topic
  - subtopic
---
```

- `tags` — array, no `#` prefix, lowercase and hyphenated (`data-structures`, not `DataStructures`)
- Add domain-specific keys only when explicitly requested
- Use `#tag` inline only where a Dataview query needs it; prefer frontmatter for static tags

**Timestamp fields.** Where the vault runs the Obsidian **Linter** plugin, `created` and `modified` are managed automatically and kept in sync with the file on disk:

- Do **not** add `created` or `modified`
- If they are already present in a note being edited, leave them exactly as they are

This is a Linter-plugin convention, not a fact about Obsidian. In a vault without the Linter, writing these fields is harmless but still usually pointless.

---

## WikiLinks

| Pattern | Usage |
|---|---|
| `[[Note Name]]` | Basic link — renders as the note name |
| `[[Note Name\|Display Text]]` | Custom display text for in-sentence linking |
| `[[Note Name#Heading]]` | Link to a specific heading |
| `![[Note Name]]` | Embed note inline |
| `![[image.png]]` | Embed an image |

When linking within a sentence, use pipe syntax so the prose reads naturally. Keep WikiLink targets specific and descriptive — they represent actual notes. Avoid vague names like `[[Concept]]`; prefer `[[Gradient Descent]]`.

Linking to a note that does not exist yet is legitimate. Obsidian renders it as an unresolved link, which is the correct signal that the note is pending — not an error to avoid.

### House link patterns

Two positions carry a fixed notation. The rules behind them are in `structure-doctrine.md`; this is how they are written.

**Pointer to a child note** — its own line, after the summary that replaced the extracted content:

```markdown
See: [[Child Topic]]
See: [[Child Topic]] | [[Related Concept]] | [[Third Note]]
```

**Related Topics** — the closing section, a flat bulleted list:

```markdown
## Related Topics

- [[Non-Functional Requirements]]
- [[Acceptance Criteria]]
```

---

## Callouts

Use sparingly — only for content the reader must not miss or that is genuinely supplementary.

```markdown
> [!note]
> Supplementary context or elaboration.

> [!tip]
> Practical advice or shortcut.

> [!warning]
> Common pitfall or caveat.

> [!important]
> Critical information the reader must not miss.
```

Common types: `note`, `tip`, `warning`, `important`, `info`, `example`, `quote`. Do not nest callouts.

### `[!model]` — Mental Model

A distillation of a complex topic into its simplest actionable form. Use on notes where a conceptual shortcut would let the reader reason about the topic without recalling all the detail.

- 3–5 bullet points maximum — each a distinct principle, not a rephrased sentence from the note body
- If it cannot be reduced to five bullets or fewer, the model is not simple enough yet
- Position: directly after the opening paragraph, before the first `##` section

```markdown
> [!model] Mental Model
> - A **secret** causes harm if leaked — externalise unconditionally, no exceptions
> - **Config** varies by environment — externalise for flexibility, not security
> - **Public values** are safe anywhere — no protection needed
> - Test: *would this value in a public commit cause harm?*
```

---

## Headings

- H1 (`#`) — note title only; one per note
- H2 (`##`) — main sections
- H3 (`###`) — subsections
- H4 (`####`) — use sparingly; prefer restructuring over deep nesting

---

## Lists

Use `-` for unordered lists consistently. Use `1.` for ordered lists when sequence matters.

---

## Emphasis

| Syntax | Use |
|---|---|
| `**bold**` | Key terms on first mention |
| `*italic*` | Titles, foreign terms, authoring guidance inside a template |
| `==highlight==` | Recall cues (Obsidian-specific) |
| `` `code` `` | Inline code, filenames, commands |

---

## Code Blocks

Always include a language identifier.

```typescript
const greet = (name: string): string => `Hello, ${name}`;
```

Common identifiers: `typescript`, `javascript`, `python`, `bash`, `yaml`, `json`, `markdown`, `sql`.

Use TypeScript for code examples unless another language is more appropriate for the domain. Prefer named types over `any`. Use `const` by default.

---

## Tables

```markdown
| Column A | Column B |
|---|---|
| Value    | Value    |
```

Use for comparisons and structured data. Left-align columns by default.

---

## Non-Obsidian targets

When writing to a plain Markdown repo, a static site, or anything destined for PDF, substitute:

| Obsidian | Elsewhere |
|---|---|
| `[[Note Name]]` | `[Note Name](./note-name.md)`, or plain text if there is no target file |
| `[[Note\|Display]]` | `[Display](./note.md)` |
| `[[Note#Heading]]` | `[Note](./note.md#heading)` |
| `![[image.png]]` | `![alt text](./path/image.png)` |
| `> [!warning]` | `> **Warning**` followed by the body |
| `==highlight==` | `**bold**` |
| YAML frontmatter | Drop it, or map to the target's metadata format |

Everything else — headings, lists, tables, code blocks, emphasis — is standard Markdown and carries over unchanged. The shape of the document does not change; only the notation does.
