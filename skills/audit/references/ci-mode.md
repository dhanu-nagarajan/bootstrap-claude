# Audit: CI Mode Specification

When running in CI/CD pipelines, the audit skill adapts its output for machine consumption.

---

## Activation

CI mode activates when:
- `--json` flag is passed
- The output is being piped (non-interactive terminal)
- Explicitly requested via config

## JSON Output Schema

```json
{
  "report_type": "audit",
  "generated_at": "2026-03-14T10:00:00Z",
  "plugin_version": "3.0.0",
  "project": "my-project",
  "compliance_score": 87,
  "threshold": 70,
  "pass": true,
  "total_rules": 45,
  "rules_checked": 42,
  "summary": {
    "critical": 2,
    "warning": 5,
    "info": 3,
    "skipped": 3
  },
  "violations": [
    {
      "id": "forbidden-eval",
      "severity": "CRITICAL",
      "rule": "No eval() usage",
      "file": "src/utils/dynamic.ts",
      "line": 23,
      "column": 5,
      "standard": "forbidden.md",
      "message": "eval() found in production code",
      "fix": "Replace with safe alternative (Function constructor or structured approach)"
    }
  ],
  "standards_coverage": {
    "forbidden.md": {"rules": 8, "checked": 8, "violations": 1},
    "architecture.md": {"rules": 4, "checked": 4, "violations": 0},
    "language.md": {"rules": 12, "checked": 10, "violations": 2},
    "testing.md": {"rules": 6, "checked": 6, "violations": 1},
    "security.md": {"rules": 8, "checked": 8, "violations": 0}
  }
}
```

## Exit Codes

| Code | Meaning |
|------|---------|
| 0 | Compliance score >= threshold |
| 1 | Compliance score < threshold |

## Threshold Configuration

Default threshold: 70

Override via:
- CLI flag: `--threshold 80`
- Config: `.bootstrap-config.yaml` → `audit.threshold: 80`

## Integration Examples

### GitHub Actions

```yaml
- name: Run bootstrap-claude audit
  run: |
    claude --plugin-dir ./path/to/bootstrap-claude -c "/audit --json --threshold 80" > audit.json
    score=$(jq '.compliance_score' audit.json)
    echo "Compliance score: $score"
    if [ $(jq '.pass' audit.json) = "false" ]; then
      echo "::error::Compliance score $score below threshold"
      exit 1
    fi
```

### PR Comment

The CI mode can also generate a PR comment summary:

```markdown
## Audit Results

**Score: 87/100** ✓ (threshold: 70)

| Severity | Count |
|----------|-------|
| Critical | 2 |
| Warning | 5 |
| Info | 3 |

<details>
<summary>Critical Violations</summary>

- `src/utils/dynamic.ts:23` — eval() found in production code (forbidden.md)
- `src/api/users.ts:45` — SQL injection risk (security.md)

</details>
```
