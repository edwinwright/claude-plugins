# Plugin mechanics

Facts about how the Claude plugin system works, not assumptions. Relevant when creating or editing `marketplace.json` or a `plugin.json`.

- **One marketplace per repo.** `marketplace.json` lives at the fixed path `.claude-plugin/marketplace.json`. There is no mechanism for multiple marketplaces in one repo.
- **Every plugin needs `.claude-plugin/plugin.json`** with a required `version` field (used to detect updates). Missing plugin.json or missing marketplace entry = won't install.
- **Skills are auto-discovered** by walking `skills/*/SKILL.md`. They are **not** declared in `plugin.json`. Same for `commands/`.
- **`category`** is a freeform string; `productivity` is used here.
- **Marketplace `name` must not contain `claude`** — Cowork rejects it as impersonating an official marketplace. See `KNOWN-ISSUES.md`.
- Plugins are not surface-specific — the same plugin works in Claude Code and Cowork. Hooks and sub-agents only run in Cowork.
- MCP servers can be bundled in a plugin, but verify the exact mechanism (`plugin.json` key vs. `.mcp.json`) against current docs before adding one.

## Keep descriptions honest and in sync

The plugin description in `marketplace.json` and in the plugin's own `plugin.json` must match and must reflect what the skills actually do. When you add or remove a skill, update both descriptions and bump `version`.
