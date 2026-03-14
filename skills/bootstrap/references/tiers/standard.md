# Standard Tier: Daily Driver

The default tier. Generates enough structure to meaningfully improve every Claude Code session without overwhelming the user. Includes everything from lite plus full standards, checklists, and context.

---

## Files to Generate

### From Lite Tier (always included)

1. `.claude/standards/forbidden.md`
2. `.claude/shield/invariants.md`
3. `.claude/shield/session-state.md`
4. `.claude/shield/recovery-protocol.md`
5. CLAUDE.md update with recovery protocol

### Standards (4 additional files)

Load the standards spec:
```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/specs/standards.md
```

Generate:
6. `.claude/standards/architecture.md` — Module boundaries, dependency rules, layer map
7. `.claude/standards/language.md` — Naming, typing, idioms, imports
8. `.claude/standards/testing.md` — Test framework, conventions, what to test
9. `.claude/standards/security.md` — Security patterns, input validation, secrets

Skip for standard tier:
- `components.md` (only in full tier, only for UI projects)
- `error-handling.md` (only in full tier)
- `performance.md` (only in full tier)

### Checklists (2 files)

Load the checklists spec:
```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/specs/checklists.md
```

Generate:
10. `.claude/checklists/pre-commit.md` — Verified build/test/lint commands + manual checks
11. `.claude/checklists/pr-review.md` — Review dimensions

Skip for standard tier:
- `new-module.md` (only in full tier)
- `incident.md` (only in full tier)

### Context (2 files)

Load the context spec:
```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/specs/context.md
```

Generate:
12. `.claude/context/decisions.md` — 3-5 inferred ADRs
13. `.claude/context/glossary.md` — Project-specific terms (5-25 entries)

Skip for standard tier:
- `domain.md` (only in full tier)

### State File

14. `.claude/.bootstrap-state.json` — With tier: "standard", validation results, fingerprint

## Files NOT Generated (Standard Tier)

- No protocols/ directory (full tier only)
- No templates/ directory (full tier only)
- No personas/ directory (full tier only)
- No standards/components.md, error-handling.md, performance.md (full tier only)
- No checklists/new-module.md, incident.md (full tier only)
- No context/domain.md (full tier only)

## Post-Generation Output

```
Done! 14 files generated in .claude/ (standard tier)

  Shield:     active (session protected)
  Standards:  {N} rules across 5 files
  Checklists: pre-commit + PR review
  Context:    {N} ADRs, {N} glossary terms
  Confidence: {score}%

  Want the full system? Run /bootstrap --full for protocols, personas, and templates.
```

## Validation

Validate all references across all generated files:
- Apply the full reference validation protocol from core/validation/
- Per-file confidence scoring
- Re-analyze any file below 70% confidence
