# Feature/Change Workflow

Four phases for any feature or modification to the plugin.

## Phase 1: Reconnaissance

- Read the relevant spec files (`skills/{name}/references/*.md`, `core/*.md`)
- Identify all cross-references: grep for the filename across the repo
- Map which skills load the affected files (see `core.md` cascade table)
- Check CLAUDE.md directory tree matches current filesystem

## Phase 2: Planning

State explicitly before executing:
- What changes and why
- Which files are affected (direct edits + cross-reference updates)
- If modifying `core/`: list every skill that loads from it
- If adding a file: where it fits in the tier system (`tiers/lite.md`, `standard.md`, `full.md`)
- If removing a file: what currently references it

## Phase 3: Execution

1. Make the primary changes
2. Update all cross-references:
   - CLAUDE.md directory tree
   - README.md (skill count, feature list, supported languages)
   - Any `SKILL.md` that references changed paths
   - `master-prompt.md` if generation flow is affected
3. If modifying `plugin.json`: bump version for user-facing changes
4. If testing locally: sync to plugin cache with rsync

## Phase 4: Verification

- Grep for old paths/names to catch stale references
- Test affected skill on a real project: `claude --plugin-dir /path/to/bootstrap-claude`
- If the change affects generation: verify output on at least one language example
- Update CHANGELOG.md if the change is user-facing
