# claude-plugins — Claude Context

A Claude plugin marketplace holding three plugins: `product-delivery` (an end-to-end SDLC pipeline), `engineering` (personal coding conventions), and `knowledge` (note and document authoring for an Obsidian vault). Built for use in my own tool chain. This repo is the single source of truth for these plugins; installed copies elsewhere should be treated as stale.

> [!warning] Public repo — no personal data
> Never commit PII, client names, credentials, private vault structure, or commercial terms. Personal-*flavoured* examples are fine and often useful — a runbook for renewing a passport shows the skills generalise past SDLC. Personal *data* is not. Review diffs for leakage before every push.

---

## Repo Shape

```
.claude-plugin/marketplace.json      catalog of every plugin
plugins/<plugin-name>/
├── .claude-plugin/plugin.json       plugin manifest — name, version, description
└── skills/<skill-name>/SKILL.md     one skill
```

Run `find plugins -name SKILL.md` for the current skill roster.

The three plugins are independent — no file in one references a file in another. There is no mechanism to declare a cross-plugin dependency, so a skill reaching into a sibling plugin fails silently when that plugin isn't installed.

---

## Before you work in a plugin, or touch plugin infrastructure

- Working within a specific plugin's skills? Read that plugin's section in `README.md` for how its skills relate to each other.
- Creating or editing `marketplace.json` or a `plugin.json`? Read `docs/plugin-mechanics.md` first.
- Writing a new skill, or restructuring/merging/splitting an existing one? Read `docs/skill-authoring.md` first.
