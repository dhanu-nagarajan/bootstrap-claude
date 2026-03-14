---
name: audit
description: >-
  This skill should be used when the user asks to "audit the codebase",
  "check compliance", "are we following standards", "check code quality",
  "run audit", "validate standards", or invokes /audit. It validates the
  current codebase against the project's .claude/standards/ files and produces
  a compliance report.
---

# Audit: Standards Compliance Check

Validate the current codebase against the project's `.claude/standards/` files, systematically checking for violations and producing a scored compliance report.

**The problem this solves:** Standards files are only useful if they are enforced. Without regular auditing, codebases drift from their own documented rules. This skill turns passive documentation into active enforcement.

**Quality bar:** Every reported violation must cite the specific rule, the specific file and line, and the exact evidence. No false positives. No vague findings.

## Execution

### Step 1: Prerequisites Check

1. Verify `.claude/standards/` exists and contains at least one `.md` file
2. If missing, tell the user: "No standards found. Run /bootstrap first to generate `.claude/standards/` files."
3. List all standard files found — these define the audit scope

### Step 2: Load and Parse Standards

Read the full audit specification:

```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/audit/references/audit-spec.md
```

For each file in `.claude/standards/`:

1. **Read the file** completely
2. **Extract testable rules** — concrete statements that can be verified via Grep, Glob, or file inspection:
   - `forbidden.md` — Each banned pattern becomes a Grep search
   - `language.md` — Naming conventions become regex checks on identifiers
   - `architecture.md` — Import/dependency rules become cross-file import analysis
   - `testing.md` — Coverage requirements become Glob checks for test file existence
   - `security.md` — Security rules become Grep searches for unsafe patterns
   - `error-handling.md` — Error handling rules become pattern checks
   - `performance.md` — Performance anti-patterns become Grep searches
3. **Skip non-testable rules** — Track abstract/subjective rules separately for the coverage report

### Step 3: Scan Codebase

Execute checks systematically. For each extracted rule:

1. **Forbidden patterns** — Use `Grep` with the forbidden pattern across all source files. Each match is a CRITICAL violation.
2. **Naming conventions** — Use `Grep` to sample 20+ identifiers (function names, variable names, class names, file names). Check each against the naming rules. Non-conforming names are WARNING violations.
3. **Architecture boundaries** — Use `Grep` to find all import/require statements. Check if any import crosses a forbidden layer boundary (e.g., presentation importing from data layer directly). Boundary violations are CRITICAL.
4. **Test coverage** — Use `Glob` to list all source files, then check each has a corresponding test file matching the project's test naming convention. Missing tests are WARNING violations.
5. **Security patterns** — Use `Grep` to search for unsafe patterns (hardcoded secrets, SQL concatenation, eval usage, etc.). Security violations are CRITICAL.
6. **Error handling** — Use `Grep` to find empty catch blocks, swallowed errors, missing error returns. Violations are WARNING.
7. **Performance anti-patterns** — Use `Grep` for known anti-patterns from the standards. Violations are INFO.

Track every finding with: rule ID, severity, file path, line number, matched text, and the rule it violates.

### Step 4: Generate Report

Write `.claude/audit-report.md` with the following structure:

```
# Audit Report -- [YYYY-MM-DD]

## Summary
- Standards checked: [count of .claude/standards/ files analyzed]
- Rules evaluated: [count of testable rules extracted]
- Violations found: [total] (Critical: [N], Warning: [N], Info: [N])
- Compliance score: [percentage]

## Critical Violations
### [N]. [Rule name] -- [standard-file.md]
- **Rule:** [exact rule text]
- **File:** `path/to/file.ext:LINE`
- **Evidence:** `matched text`
- **Fix:** [specific remediation]

## Warnings
[Same format as Critical]

## Info
[Same format as Critical]

## Standards Coverage
| Standard File | Testable Rules | Abstract Rules | Rules Checked |
|---|---|---|---|
| forbidden.md | N | N | N |
| ... | ... | ... | ... |
```

Compute compliance score: `((total_rules - critical*3 - warning*1) / total_rules) * 100`, floored at 0.

### Step 5: Summary

Present to the user:
1. The compliance score with a one-line assessment
2. Top 3 most critical findings with file locations
3. Actionable next steps: which violations to fix first
4. If compliance is above 90%, congratulate and note areas of strength

### CI Mode

When invoked with `--json` or in a CI environment, output machine-readable JSON instead of markdown:

```json
{
  "report_type": "audit",
  "generated_at": "ISO-date",
  "plugin_version": "3.0.0",
  "project": "project-name",
  "compliance_score": 87,
  "threshold": 70,
  "pass": true,
  "summary": {
    "critical": 2,
    "warning": 5,
    "info": 3
  },
  "violations": [
    {
      "severity": "CRITICAL",
      "rule": "No eval() usage",
      "file": "src/utils/dynamic.ts",
      "line": 23,
      "standard": "forbidden.md"
    }
  ]
}
```

Exit codes for CI:
- `0` — Compliance score >= threshold (default 70)
- `1` — Compliance score < threshold

### Compliance Badge

After generating the audit report, also generate `.claude/audit-badge.md`:

```markdown
<!-- Add this to your README.md -->
![bootstrap-claude compliance](https://img.shields.io/badge/bootstrap--claude-{score}%25_compliant-{color})
```

Color mapping:
- Score >= 90: `brightgreen`
- Score >= 75: `green`
- Score >= 60: `yellow`
- Score >= 40: `orange`
- Score < 40: `red`

## Quality Principles

1. **Evidence-based** — Every violation cites file, line, and matched text
2. **Zero false positives** — Only report what is definitively a rule violation
3. **Actionable** — Every finding includes a specific fix
4. **Severity-accurate** — CRITICAL means bugs or security risk, WARNING means quality degradation, INFO means style preference
5. **Complete** — Check every testable rule, do not skip or sample
6. **Transparent** — Report which rules could not be automatically checked

**The test:** Could a developer fix every reported violation without asking clarifying questions?

## Additional Resources

### Reference Files

The complete audit methodology and rule extraction specifications are in:
- **`references/audit-spec.md`** — How to extract testable rules from each standard type, scoring methodology, and severity classification
