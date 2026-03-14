# Standardized Report Format

All skills that generate reports MUST follow this format.

---

## Report Structure

### Header

```markdown
# {Report Type}: {project-name}

**Generated:** {ISO date} by bootstrap-claude v{version}
**Skill:** /{skill-name}
```

### Sections

Each report section follows:

```markdown
## {Section Title}

{Content — tables, lists, or prose as appropriate}
```

### Severity Indicators

When reporting issues, use consistent severity:

| Severity | Label | Weight | Meaning |
|----------|-------|--------|---------|
| CRITICAL | 🔴 | 3x | Causes bugs, security issues, or data loss |
| WARNING | 🟡 | 1x | Affects code quality, may cause issues |
| INFO | 🔵 | 0x | Informational, no action required |

### Score Tables

```markdown
| Category | Score | Status |
|----------|-------|--------|
| Architecture | 95% | ✓ |
| Testing | 72% | ⚠ |
| Security | 100% | ✓ |
```

### Actionable Items

End every report with:

```markdown
## Recommended Actions

1. **{Action}** — {why} (severity: {CRITICAL/WARNING/INFO})
2. **{Action}** — {why} (severity: {CRITICAL/WARNING/INFO})
```

## Report Types

### Audit Report (`.claude/audit-report.md`)
Sections: Summary, Violations by Severity, Compliance Score, Per-Standard Coverage, Recommended Actions

### Doctor Report (`.claude/doctor-report.md`)
Sections: Summary, Broken References, Coverage Gaps, Health Status, Recommended Actions

### Roast Report (`.claude/roast-report.md`)
Sections: Overall Score, Dimension Scores, The Good, The Bad, The Ugly, Quick Wins, Shareable Summary

## Machine-Readable Output

When `--json` flag is used, output JSON instead of markdown:

```json
{
  "report_type": "audit",
  "generated_at": "2026-03-14T10:00:00Z",
  "plugin_version": "3.0.0",
  "project": "my-project",
  "summary": { ... },
  "findings": [ ... ],
  "score": 87,
  "recommended_actions": [ ... ]
}
```
