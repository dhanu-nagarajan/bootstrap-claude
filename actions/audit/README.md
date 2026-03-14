# bootstrap-claude Audit Action

Run bootstrap-claude standards compliance audit on pull requests.

## Usage

```yaml
name: Standards Audit
on:
  pull_request:
    branches: [main]

jobs:
  audit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Run audit
        uses: dhanu-nagarajan/bootstrap-claude/actions/audit@v3
        with:
          claude-api-key: ${{ secrets.ANTHROPIC_API_KEY }}
          threshold: 80
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `claude-api-key` | Yes | — | Anthropic API key |
| `threshold` | No | `70` | Minimum score to pass |
| `standards-path` | No | `.claude` | Path to standards directory |
| `post-comment` | No | `true` | Post results as PR comment |

## Outputs

| Output | Description |
|--------|-------------|
| `score` | Compliance score (0-100) |
| `pass` | Whether score meets threshold |
| `report` | Path to JSON report |
| `violations-critical` | Count of critical violations |
| `violations-warning` | Count of warning violations |

## Prerequisites

Run `/bootstrap` on your project first to generate the `.claude/` standards directory.

## Example PR Comment

The action posts a comment on the PR with the audit results:

> ## :white_check_mark: Audit Passed
> **Score: 87/100** (threshold: 70)
>
> | Severity | Count |
> |----------|-------|
> | Critical | 0 |
> | Warning | 3 |
> | Info | 5 |
