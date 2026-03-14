# Audit: Compliance Badge Specification

Generate a compliance badge for project READMEs after running `/audit`.

---

## Badge Format

Uses shields.io static badge format:

```
https://img.shields.io/badge/bootstrap--claude-{score}%25_compliant-{color}
```

## Color Mapping

| Score Range | Color | Hex |
|-------------|-------|-----|
| 90-100 | brightgreen | #4c1 |
| 75-89 | green | #97CA00 |
| 60-74 | yellow | #dfb317 |
| 40-59 | orange | #fe7d37 |
| 0-39 | red | #e05d44 |

## Generated File

After audit, create `.claude/audit-badge.md`:

```markdown
# Compliance Badge

Add this to your README.md to show your compliance score:

```markdown
[![bootstrap-claude compliance](https://img.shields.io/badge/bootstrap--claude-{score}%25_compliant-{color})](https://github.com/dhanu-nagarajan/bootstrap-claude)
```

**Last audit:** {date}
**Score:** {score}/100
**Skill:** `/audit` from bootstrap-claude v3.0.0
```

## Badge Update

The badge in `.claude/audit-badge.md` is regenerated every time `/audit` runs.
The badge URL is static (shields.io) — the score value is embedded in the URL.
To show a live badge, users would need to set up a CI job that updates the badge URL in their README.

## Usage

Users copy the badge markdown from `.claude/audit-badge.md` into their project's README.
Every badge displayed is a distribution channel for bootstrap-claude.
