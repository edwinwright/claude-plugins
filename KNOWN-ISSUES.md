# Known Issues

Tracking environment-specific problems with installing these plugins. Issues here are **not** repo defects — the marketplace, manifests, and skills are correct and install cleanly in Claude Code.

---

## Cowork: marketplace/plugin state is remote-synced and can't be managed locally

**Status:** Blocked on an upstream Cowork bug — no repo-side fix.

**Symptoms**

- Plugins added in Cowork can't be fully removed from the UI (only individual plugins are listed, no marketplace to remove).
- A removed plugin (e.g. `knowledge/ops-docs`) reappears after restart.
- Deleting caches on disk has no effect — the plugin comes back on the next sync.

**Root cause**

Cowork migrated plugin/marketplace state to a **remote, account-level store** (`remote_marketplace_migration_done_v1: true` in `~/Library/Application Support/Claude/config.json`). `RemotePluginManager` syncs this down on every cycle and on restart, so local edits are always overwritten. There is no local config file to edit.

Local paths that look relevant but are **not** the source of truth:

- `~/.claude/plugins/` — Claude Code's store, not Cowork's.
- `~/Library/Application Support/Claude/local-agent-mode-sessions/**` — regenerated per-session working copies.

**Upstream reports**

- [anthropics/claude-code#38429 — RemotePluginManager removes 3rd-party GitHub marketplace plugins on every sync](https://github.com/anthropics/claude-code/issues/38429)
- [anthropics/claude-code#40475 — personal marketplace plugin removed by sync on every cycle](https://github.com/anthropics/claude-code/issues/40475)
- [anthropics/claude-code#40600 — personal marketplace plugin installation lost after app restart](https://github.com/anthropics/claude-code/issues/40600)

**Workarounds**

1. **Use Claude Code** for these plugins — the same repos install without the remote-sync layer:
   ```
   /plugin marketplace add edwinwright/claude-plugins
   /plugin install knowledge
   /plugin install delivery-design
   ```
2. To force Cowork to re-pull after a repo change, **reinstall the specific plugin** from Cowork's plugin settings (remove/disable, then re-enable). This is the only "force re-sync" lever the UI offers.

---

## Fixed: `knowledge` plugin showed no skills

**Status:** Fixed (commit `2e7be7c`).

Skills were placed directly under `plugins/knowledge/` instead of `plugins/knowledge/skills/`. Skills are auto-discovered by walking `skills/*/SKILL.md`, so they weren't found. Moving `obsidian-note-writer`, `session-synthesis`, and `zettelkasten` under `skills/` resolved it. (`zettelkasten` has since been merged into `note-formats`.)

> [!note] Cowork may lag
> Because of the remote-sync issue above, Cowork can keep showing the pre-fix state until the `knowledge` plugin is reinstalled from its settings.

---

## Cowork: renamed skills can appear alongside their old names

**Status:** A consequence of the remote-sync issue above, not a separate bug.

`delivery-design` 2.0.0 renamed eight skills. Because Cowork syncs plugin state from a remote store rather than from disk, an install can end up carrying both the old and the new skill sets at once — `feature-brief` and `backlog-refinement` both loaded, competing for the same trigger phrases.

**Symptom:** a skill fires under a name that no longer exists in this repo, or two skills with overlapping descriptions both appear in the roster.

**Fix:** reinstall the plugin from Cowork's plugin settings (remove/disable, then re-enable). That is the only force-resync lever the UI offers.

Claude Code has no remote-sync layer and picks the rename up directly, so verify there first if you want to know whether the repo itself is correct.

---

## Notes

- Marketplace `name` fields must not contain `claude` — Cowork rejects them as impersonating an official marketplace. This repo uses `edwinwright-plugins`.
- There is **no alias mechanism** for skill names. Skills are discovered by walking `skills/*/SKILL.md`, and the directory name must match the frontmatter `name`. Keeping an old name alive means shipping a stub skill directory, whose description then loads into every session in every project. Renames here are therefore hard renames.
