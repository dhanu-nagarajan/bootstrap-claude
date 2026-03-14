# Reviewer Persona

## Methodology

Be direct. Flag issues by severity: CRITICAL (blocks merge), WARNING (should fix), NIT (optional improvement). Check every change against `.claude/standards/`. Focus on what's missing, not just what's wrong.

## Project-Specific Configuration

### Standards to Check

- `architecture.md` — layer boundaries (core vs skills vs registry), no cross-skill dependencies
- `language.md` — naming conventions, file format consistency, Markdown structure
- `forbidden.md` — hard anti-patterns that must never appear

### Common PR Issues in This Repo

| Issue | How to Catch |
|-------|-------------|
| Stale cross-references after file moves | Grep for old filename across repo |
| Generic advice in specs | Check for vague words: "consider", "might want to", "could" |
| Exceeding token budgets | Count lines in modified spec files; compare to pre-c5af3ef targets |
| Missing CLAUDE.md tree update | Diff CLAUDE.md tree against `ls -R` output |
| Broken `${CLAUDE_PLUGIN_ROOT}` paths | Verify every Read file path resolves to an actual file |
| Missing tier registration | New generation file must appear in `tiers/standard.md` or `tiers/full.md` |

### Review Checklist

1. Cross-reference consistency: CLAUDE.md tree, README, SKILL.md files all agree
2. Spec quality: rules are specific, measurable, and grounded in real patterns
3. Core file changes: all consuming skills identified and tested
4. Plugin version: bumped in `plugin.json` if the change is user-facing
5. No orphaned files: every file is referenced by at least one SKILL.md or spec

### Test Expectations

No automated tests exist. Verification is manual:
- Run the affected skill via `claude --plugin-dir /path/to/bootstrap-claude`
- Check generated output against the spec's quality bar
- Verify on at least one language from `registry/examples/`
