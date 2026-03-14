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

This skill operates in two modes:

1. **Plugin self-update** — When the user says "update plugin", "upgrade bootstrap", "upgrade", "check for updates", or "is bootstrap up to date"
2. **Standards update** — When the user wants to refresh `.claude/` files to match codebase evolution (default)

---

## Mode Detection

Parse the user's request:
- If it contains "plugin", "bootstrap-claude", "upgrade", "check for updates", "up to date", or "latest version" → **Plugin Self-Update mode**
- Otherwise → **Standards Update mode**

---

## Plugin Self-Update Mode

### Step 1: Check Current Version

Read the local plugin version:

```
Read file: ${CLAUDE_PLUGIN_ROOT}/.claude-plugin/plugin.json
```

Extract the `"version"` field. Tell the user their current version.

### Step 2: Check Remote Version

Fetch the latest version from the repository:

```
WebFetch: https://raw.githubusercontent.com/dhanu-nagarajan/bootstrap-claude/main/.claude-plugin/plugin.json
```

Extract the `"version"` field from the response.

If the fetch fails, tell the user: "Could not reach the update server. Check your network connection or visit https://github.com/dhanu-nagarajan/bootstrap-claude to check manually."

### Step 3: Compare & Report

Compare versions using semantic versioning:

**If up to date (versions match):**
```
✅ bootstrap-claude is up to date (v{version})
```

**If update available (remote is newer):**
```
🔄 Update available: v{local} → v{remote}
```

Then fetch the changelog to show what's new:

```
WebFetch: https://raw.githubusercontent.com/dhanu-nagarajan/bootstrap-claude/main/CHANGELOG.md
```

If a changelog exists, show the entries between the local version and the remote version. If no changelog exists, skip this step.

### Step 4: Apply Update

If an update is available, apply it:

1. Determine the plugin install location from `${CLAUDE_PLUGIN_ROOT}`
2. Run `git -C "${CLAUDE_PLUGIN_ROOT}" pull origin main` to pull the latest changes
3. If git pull succeeds:
   ```
   ✅ bootstrap-claude updated to v{remote}

   Changes pulled. The new version is active immediately — no restart needed.
   ```
4. If git pull fails (not a git repo, merge conflicts, etc.), fall back to reinstall instructions:
   ```
   ⚠️ Automatic update failed. To update manually:

   1. Remove the current plugin:
      /plugin uninstall bootstrap-claude

   2. Reinstall from marketplace:
      /plugin install bootstrap-claude@bootstrap-claude
   ```

### Step 5: Post-Update Notice

After a successful update, inform the user:
- If the new version includes new skills, list them
- If any skill specifications changed, suggest re-running `/bootstrap` to regenerate `.claude/` files with the improved specs
- If shield specs changed, suggest running `/shield` to refresh compaction protection

---

## Standards Update Mode

Incrementally update `.claude/` files to reflect codebase evolution. Detect what changed, preserve user customizations, apply targeted updates, and maintain a state record for future diffs.

**The problem this solves:** Running /bootstrap again would overwrite user customizations and miss incremental changes. The /update skill surgically updates only what drifted while preserving everything the user intentionally modified.

**Quality bar:** User-edited sections must be perfectly preserved. Updates must be accurate reflections of actual codebase state. The diff between old and new content must be reviewable before applying.

### Step 0: Version Check

Before updating standards, check if the plugin itself is outdated:

```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/version-check.md
```

Follow the version check protocol. If the plugin is outdated, show the update notice before proceeding with the standards update. Do not block — proceed with the update regardless.

### Step 1: Run Doctor

Execute the doctor workflow internally to get the current drift report.

Read the doctor specification:

```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/doctor/references/doctor-spec.md
```

Perform the full doctor analysis (Steps 2-4 from /doctor) but do not write the doctor report file. Capture findings in memory:
- All broken references (file paths, commands, code symbols)
- All coverage gaps (new directories, dependencies, file patterns)
- Stale content indicators
- Bootstrap state comparison (if state file exists)

### Step 2: Triage Changes

Read the update specification:

```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/update/references/update-spec.md
```

Categorize every finding into one of three tiers:

**Auto-fixable (apply without asking):**
- Broken file path references where the file moved (update path in place)
- Renamed commands where the new name is clear (update command in place)
- New dependencies that need a mention added to relevant standards
- Updated config values (e.g., Node version, TypeScript target)

**Requires re-analysis (apply with review):**
- New modules needing architecture.md sections
- Changed testing patterns needing testing.md updates
- New file types needing language.md coverage
- Significant dependency additions needing standards updates

**Manual review needed (flag for user):**
- Architecture changes (new layers, changed boundaries)
- Framework migrations (different rendering strategy, new ORM)
- Removal of major components
- Changes that conflict with user-edited sections

### Step 3: Apply Updates

For each `.claude/` file that needs changes:

1. **Read the current file** completely
2. **Detect user-edited sections** — Look for `<!-- user-edited -->` markers. Everything between `<!-- user-edited -->` and `<!-- /user-edited -->` is preserved exactly as-is.
3. **Generate updated content** for non-user-edited sections:
   - For broken references: replace old paths/commands with correct ones
   - For coverage gaps: add new sections covering the missing elements
   - For stale content: re-analyze the relevant codebase section and regenerate
4. **Show diff to user** — Present the proposed changes as a before/after comparison for each file. Wait for confirmation before applying changes in the "requires re-analysis" tier.
5. **Apply changes** — Use Edit tool to make targeted replacements. Never rewrite entire files; patch only what changed.

**Update strategies by file type:**

| File | Strategy | Notes |
|---|---|---|
| `architecture.md` | Additive | Add new module sections, do not remove existing ones |
| `language.md` | Additive | Add conventions for new patterns, preserve existing rules |
| `testing.md` | Additive | Add new test patterns, update commands if changed |
| `forbidden.md` | Preserve | Only add, never remove forbidden patterns |
| `security.md` | Preserve | Only add, never remove security rules |
| `error-handling.md` | Replace sections | Update to match current error handling patterns |
| `performance.md` | Additive | Add new anti-patterns found |
| Checklists | Replace commands | Update commands to match current package.json/Makefile |
| Personas | Preserve | Rarely need updating; flag if architecture changed significantly |
| Context files | Additive | Add new decisions, glossary terms, domain concepts |

### Step 4: Update State

Write or update `.claude/.bootstrap-state.json`:

```json
{
  "version": "2.0.0",
  "plugin_version": "2.0.0",
  "last_bootstrap": "[preserved from existing or set to now if first run]",
  "last_update": "[current ISO date]",
  "files_generated": ["list of all .claude/ files"],
  "files_updated": ["list of files modified in this run"],
  "analysis_fingerprint": "[SHA-256 of current codebase state]",
  "user_edited_sections": ["list of preserved user-edited section identifiers"]
}
```

Compute the analysis fingerprint using the method defined in the doctor spec: hash of file tree + package manifest + config files.

### Step 5: Update Shield

If `.claude/shield/` exists:

1. Read `.claude/shield/invariants.md`
2. Check if any rules changed due to updates (new forbidden patterns, changed architecture boundaries)
3. If rules changed, regenerate `invariants.md` with updated rules while preserving the existing structure and any user-added rules
4. Update the shield's session-state template if new modules or commands were added

### Step 6: Summary

Report to the user:

1. **Files updated** — List each file with a one-line description of what changed
2. **User sections preserved** — Confirm which user-edited sections were kept intact
3. **Manual review items** — List any changes that need human decision-making
4. **State recorded** — Confirm the bootstrap state was updated with new fingerprint
5. **Next steps** — Suggest running /audit to verify compliance after updates

## Quality Principles

1. **Non-destructive** — User customizations are sacred; never overwrite `<!-- user-edited -->` sections
2. **Surgical** — Change only what needs changing; do not regenerate entire files
3. **Transparent** — Show diffs before applying non-trivial changes
4. **Idempotent** — Running /update twice in a row produces no changes the second time
5. **Traceable** — State file records exactly what was done and when
6. **Conservative** — When uncertain whether something changed intentionally, flag for review rather than auto-fixing

**The test:** After /update, do all `.claude/` files accurately reflect the current codebase while preserving every intentional user customization?

## Additional Resources

### Reference Files

The complete update methodology is in:
- **`references/update-spec.md`** — User-edited section detection, merge strategies, fingerprint computation, and rollback guidance
