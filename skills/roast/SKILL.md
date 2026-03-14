---
name: roast
description: >-
  This skill should be used when the user asks to "roast my code",
  "roast this codebase", "rate my code", "score my project",
  "how bad is my code", "codebase quality", "grade my code",
  or invokes /roast. It generates a shareable codebase quality
  report with dimension scores, specific findings, and actionable
  quick wins.
---

# Roast: Codebase Quality Report

Generate a shareable, scored analysis of codebase quality. The tone is direct and constructive — honest assessment with actionable feedback, not corporate vagueness.

**Quality bar:** Every finding must reference specific files with line numbers. No hand-waving. Scores must be evidence-based, derived from measurable patterns.

## Execution

### Step 1: Deep Analysis

Read the shared analysis protocol:

```
Read file: ${CLAUDE_PLUGIN_ROOT}/core/analysis/codebase-analyzer.md
```

Perform full codebase analysis. Then evaluate across 6 dimensions:

1. **Architecture** — Separation of concerns, dependency hygiene, module boundaries, circular dependencies
2. **Code Quality** — Naming consistency, typing discipline, dead code, code duplication, function length
3. **Testing** — Test-to-source ratio, test quality, coverage of critical paths, fixture patterns
4. **Security** — Input validation, secrets management, dependency audit, injection risks
5. **DX (Developer Experience)** — Documentation, onboarding friction, dev tooling, error messages
6. **Anti-patterns** — Forbidden pattern density, code smells, tech debt markers (TODO/FIXME/HACK)

### Step 2: Score Each Dimension

Score 0-100 per dimension using evidence, not vibes:

**Architecture (0-100):**
- Check: clear module boundaries exist → +20
- Check: dependency direction is enforced or consistent → +20
- Check: no circular imports detected → +20
- Check: separation of concerns (handlers don't contain business logic) → +20
- Check: consistent architecture pattern used → +20

**Code Quality (0-100):**
- Check: consistent naming conventions → +20
- Check: type safety enforced (strict mode, type annotations) → +20
- Check: no dead code / unused exports → +20
- Check: functions under 50 lines average → +20
- Check: no code duplication (DRY) → +20

**Testing (0-100):**
- Check: test files exist → +20
- Check: test-to-source ratio > 0.3 → +20
- Check: critical paths tested (auth, payments, data mutations) → +20
- Check: tests are meaningful (not just smoke tests) → +20
- Check: test infrastructure is maintained (not broken/skipped) → +20

**Security (0-100):**
- Check: no hardcoded secrets in source → +20
- Check: input validation at boundaries → +20
- Check: no SQL injection / XSS / injection patterns → +20
- Check: dependencies not severely outdated → +20
- Check: auth/authz patterns present where needed → +20

**DX (0-100):**
- Check: README with setup instructions → +20
- Check: consistent dev tooling (linter, formatter, type checker) → +20
- Check: clear error messages in custom errors → +20
- Check: CI/CD configuration exists → +20
- Check: contribution guide or dev docs → +20

**Anti-patterns (0-100, inverted — fewer is better):**
- Start at 100
- Each TODO/FIXME/HACK: -2 (cap at -30)
- Each forbidden pattern instance: -5
- Each deeply nested function (>4 levels): -3
- Each overly long file (>500 lines): -3
- Each `any`/`type: ignore`/`unsafe` without justification: -5

### Step 3: Calculate Overall Score

```
overall = (architecture + code_quality + testing + security + dx + anti_patterns) / 6
```

Grade mapping:
| Score | Grade | Emoji |
|-------|-------|-------|
| 90-100 | A | 🏆 |
| 80-89 | B | 💪 |
| 70-79 | C+ | 👍 |
| 60-69 | C | 😐 |
| 50-59 | D | 😬 |
| 0-49 | F | 🔥 |

### Step 4: Generate Report

Write `.claude/roast-report.md`:

```markdown
# Codebase Roast: {project-name}

**Overall Score: {score}/100 {grade}** {emoji}
**Generated:** {date} by bootstrap-claude v3.0.0 `/roast`

---

## Dimension Scores

| Dimension | Score | Grade | Verdict |
|-----------|-------|-------|---------|
| Architecture | {score} | {grade} | {one-line verdict} |
| Code Quality | {score} | {grade} | {one-line verdict} |
| Testing | {score} | {grade} | {one-line verdict} |
| Security | {score} | {grade} | {one-line verdict} |
| DX | {score} | {grade} | {one-line verdict} |
| Anti-patterns | {score} | {grade} | {one-line verdict} |

---

## The Good

{2-3 specific things the codebase does well, with file references}

## The Bad

{3-5 specific issues, ranked by impact, with file:line references}

## The Ugly

{1-2 critical issues that need immediate attention, with evidence}

---

## Top 5 Quick Wins

1. **{Specific actionable fix}** — Impact: {high/medium}, Effort: {low/medium}
   Files: {file references}

2. ...

---

## Shareable Summary

> "{project-name} scores {score}/100 on bootstrap-claude's /roast.
> Strengths: {top strength}. Biggest gap: {top weakness}.
> Roast your own codebase: github.com/dhanu-nagarajan/bootstrap-claude"
```

### Step 5: Present Results

Display the full report to the user. End with:
- "Share your score — copy the Shareable Summary above"
- "Fix the top issues? Run `/bootstrap` to generate enforced standards"
- "Track improvement? Run `/roast` again after changes"

## Quality Principles

1. **Evidence-based scoring** — Every point gained or lost references specific code
2. **Honest but constructive** — Direct about problems, actionable about solutions
3. **Calibrated** — A "real" project should score 60-80. 90+ is genuinely excellent. Below 40 is genuinely concerning.
4. **Shareable** — The summary line should be tweetable and include a link back to the tool
5. **Actionable** — Quick wins should be specific enough to implement immediately

**The test:** Would a developer share their roast score with their team?

## Additional Resources

### Reference Files
- **`${CLAUDE_PLUGIN_ROOT}/core/analysis/codebase-analyzer.md`** — Shared analysis protocol
- **`${CLAUDE_PLUGIN_ROOT}/core/output/report-format.md`** — Report formatting standard
- **`references/roast-spec.md`** — Detailed scoring rubric and calibration
