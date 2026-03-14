# Standardized Report Format

## Severity System

| Severity | Label | Weight | Meaning |
|----------|-------|--------|---------|
| CRITICAL | 🔴 | 3x | Causes bugs, security issues, or data loss |
| WARNING | 🟡 | 1x | Affects code quality, may cause issues |
| INFO | 🔵 | 0x | Informational, no action required |

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
