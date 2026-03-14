# Incident Response Checklist

> Adapted for a Claude Code plugin (not a deployed service). "Incident" = broken plugin release.

## 1. Assess

- [ ] Identify what is broken: which skill? which spec?
- [ ] Determine severity: does it affect generation or just reporting?
- [ ] Check if it blocks all users or only specific project types/languages
- [ ] Note the last known good version/commit

## 2. Investigate

- [ ] Review recent commits since last known good state
- [ ] Check if the issue is in `core/` (affects all skills) or isolated to one skill
- [ ] Reproduce the issue locally with `claude --plugin-dir .`
- [ ] Compare broken output with expected output from showcase examples

## 3. Fix

- [ ] Patch the affected spec or skill file
- [ ] Verify the fix does not break other skills (especially if `core/` was changed)
- [ ] Test locally with `claude --plugin-dir .` against a sample project
- [ ] Confirm shield files still generate correctly (they protect every session)

## 4. Release

- [ ] Commit fix with imperative message (e.g., "Fix validation scoring in audit spec")
- [ ] Push to main branch
- [ ] Sync to plugin cache via rsync (NOT `git pull` — cache is not a git repo)
- [ ] Verify updated plugin loads correctly via `/reload-plugins`

## 5. Post-Incident

- [ ] Update `CHANGELOG.md` with fix description
- [ ] Consider adding validation to prevent recurrence
- [ ] If a forbidden pattern caused the issue, add it to `invariants.md`
- [ ] If a spec was ambiguous, clarify the wording to prevent misinterpretation
