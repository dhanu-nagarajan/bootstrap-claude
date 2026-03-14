---
name: update
description: >-
  This skill should be used when the user asks to "update standards",
  "refresh .claude", "bootstrap update", "sync standards", "update .claude files",
  "regenerate standards", "update .claude", "update plugin", "update bootstrap",
  "upgrade plugin", "upgrade bootstrap-claude", "check for updates",
  "is bootstrap up to date", or invokes /update. It either updates .claude/ files
  to match codebase evolution, or self-updates the bootstrap-claude plugin itself
  when the user says "update plugin" or "upgrade".
---

# Update: .claude/ Maintenance & Plugin Self-Update

Two modes:

1. **Plugin self-update** — When request contains "plugin", "bootstrap-claude", "upgrade", "check for updates", "up to date", or "latest version"
2. **Standards update** — Everything else (default)

---

## Plugin Self-Update Mode

### Step 1: Check Current Version

```
Read file: ${CLAUDE_PLUGIN_ROOT}/.claude-plugin/plugin.json
```

Extract `"version"`. Tell user their current version.

### Step 2: Check Remote Version

```
WebFetch: https://raw.githubusercontent.com/dhanu-nagarajan/bootstrap-claude/main/.claude-plugin/plugin.json
```

Extract `"version"`. If fetch fails: "Could not check for updates. Visit https://github.com/dhanu-nagarajan/bootstrap-claude manually."

### Step 3: Compare & Report

- Versions match → `✅ bootstrap-claude is up to date (v{version})`
- Remote newer → show update notice, fetch changelog:
  ```
  WebFetch: https://raw.githubusercontent.com/dhanu-nagarajan/bootstrap-claude/main/CHANGELOG.md
  ```

### Step 4: Apply Update

The plugin cache (`${CLAUDE_PLUGIN_ROOT}`) is NOT a git repo — it's a filesystem snapshot managed by Claude Code's plugin system. Do NOT attempt `git pull`.

**Update strategy (in order of preference):**

1. **Try rsync from local clone.** Check if a local clone exists at common locations:
   ```bash
   # Check common locations for a local clone
   for dir in \
     "$HOME/Documents/Github/bootstrap-claude" \
     "$HOME/bootstrap-claude" \
     "$HOME/projects/bootstrap-claude" \
     "$HOME/code/bootstrap-claude" \
     "$HOME/dev/bootstrap-claude"; do
     if [ -f "$dir/.claude-plugin/plugin.json" ]; then
       echo "$dir"
       break
     fi
   done
   ```
   If found and its version matches remote, sync:
   ```bash
   rsync -av --delete --exclude='.git' --exclude='.orphaned_at' "$LOCAL_CLONE/" "${CLAUDE_PLUGIN_ROOT}/"
   ```

2. **Try downloading from GitHub.** Clone to a temp directory and copy:
   ```bash
   TMPDIR=$(mktemp -d)
   git clone --depth 1 https://github.com/dhanu-nagarajan/bootstrap-claude.git "$TMPDIR/bootstrap-claude"
   if [ $? -eq 0 ]; then
     rsync -av --delete --exclude='.git' "$TMPDIR/bootstrap-claude/" "${CLAUDE_PLUGIN_ROOT}/"
     rm -rf "$TMPDIR"
   fi
   ```

3. **Fallback: tell user to reinstall.**
   ```
   ⚠️ Automatic update failed. To update manually:

   /plugin uninstall bootstrap-claude
   /install-plugin https://github.com/dhanu-nagarajan/bootstrap-claude
   ```

After successful update:
```
✅ bootstrap-claude updated to v{remote}
   Run /reload-plugins to activate the new version.
```

### Step 5: Post-Update Notice

After update, tell the user:
- Run `/reload-plugins` to activate new version in current session
- If new skills were added, list them
- Suggest re-running `/bootstrap` if generation specs changed
- Suggest `/shield` if shield specs changed

---

## Standards Update Mode

Incrementally update `.claude/` files to reflect codebase evolution. Surgically update only what drifted while preserving user customizations.

### Step 0: Version Check

```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/version-check.md
```

Show update notice if outdated. Do not block.

### Step 1: Run Doctor

```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/doctor/references/doctor-spec.md
```

Run full doctor analysis internally (don't write report file). Capture:
- Broken references (file paths, commands, code symbols)
- Coverage gaps (new directories, dependencies, file patterns)
- Stale content indicators
- Bootstrap state comparison (if state file exists)

### Step 2: Triage Changes

```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/update/references/update-spec.md
```

Categorize findings:

**Auto-fixable (apply without asking):**
- Broken paths where file moved (update path)
- Renamed commands (update command)
- New dependencies needing mention
- Updated config values

**Requires re-analysis (show diff, ask before applying):**
- New modules needing architecture.md sections
- Changed testing patterns
- New file types needing language.md coverage
- Significant new dependencies

**Manual review (flag for user):**
- Architecture changes (new layers, changed boundaries)
- Framework migrations
- Removal of major components
- Conflicts with user-edited sections

### Step 3: Apply Updates

For each `.claude/` file needing changes:

1. Read the current file
2. Detect `<!-- user-edited -->` / `<!-- /user-edited -->` markers — preserve exactly
3. Generate updated content for non-user-edited sections
4. Show diff for "requires re-analysis" tier, wait for confirmation
5. Apply with Edit tool — patch only what changed, never rewrite entire files

**Strategies by file type:**

| File | Strategy |
|---|---|
| `architecture.md` | Additive — add modules, don't remove |
| `language.md` | Additive — add conventions, keep existing |
| `testing.md` | Additive — add patterns, update commands |
| `forbidden.md` | Append-only — never remove |
| `security.md` | Append-only — never remove |
| `error-handling.md` | Replace sections |
| Checklists | Replace commands to match current manifest |
| Personas | Preserve — flag if architecture changed |
| Context files | Additive — add decisions, terms, concepts |

### Step 4: Update State

Write `.claude/.bootstrap-state.json` with updated fingerprint, file lists, and timestamp.

### Step 5: Update Shield

If `.claude/shield/` exists and rules changed:
1. Regenerate `invariants.md` with updated rules, preserving user-added rules
2. Update session-state template if new modules/commands added

### Step 6: Summary

Report: files updated, user sections preserved, manual review items, next steps (suggest `/audit`).

## Quality Principles

1. **Non-destructive** — Never overwrite `<!-- user-edited -->` sections
2. **Surgical** — Change only what needs changing
3. **Transparent** — Show diffs before applying non-trivial changes
4. **Idempotent** — Running twice produces no changes the second time
5. **Conservative** — When uncertain, flag for review rather than auto-fixing

## Additional Resources

- **`references/update-spec.md`** — User-edited section detection, merge strategies, fingerprint computation
