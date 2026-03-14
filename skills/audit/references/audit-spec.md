# Audit Specification: Rule Extraction and Compliance Scoring

This document defines how to extract testable rules from `.claude/standards/` files, execute checks, classify severity, and compute compliance scores.

## Rule Extraction by Standard Type

### From `forbidden.md`

Forbidden files contain explicit banned patterns. Each entry maps directly to a Grep search.

**Extraction method:**
1. Read the file and identify every line that describes a banned practice
2. For each ban, derive a regex pattern that would match violating code
3. Common patterns to extract:
   - `// @ts-ignore` or `// @ts-expect-error` without explanation
   - `any` type usage (regex: `:\s*any[^a-zA-Z]`)
   - `console.log` left in production code
   - Direct DOM manipulation in framework code
   - Hardcoded credentials (regex: `(password|secret|api_key)\s*=\s*['"][^'"]+['"]`)
   - Disabled linter rules (regex: `eslint-disable|noinspection|noqa`)

**Severity:** All forbidden pattern violations are CRITICAL.

### From `language.md`

Language standards define naming, typing, and style conventions.

**Extraction method:**
1. Identify naming conventions (camelCase, PascalCase, snake_case, SCREAMING_SNAKE)
2. Map each to the code element it applies to:
   - Functions/methods: check with `Grep` for function declarations
   - Classes/types: check with `Grep` for class/type/interface declarations
   - Constants: check with `Grep` for const declarations with ALL_CAPS expectation
   - Files: check with `Glob` for file naming patterns
3. Build regex patterns per convention:
   - camelCase: `^[a-z][a-zA-Z0-9]*$`
   - PascalCase: `^[A-Z][a-zA-Z0-9]*$`
   - snake_case: `^[a-z][a-z0-9_]*$`
   - SCREAMING_SNAKE: `^[A-Z][A-Z0-9_]*$`

**Severity:** Naming violations are WARNING. Type safety violations (e.g., missing return types) are WARNING.

### From `architecture.md`

Architecture standards define layer boundaries and dependency directions.

**Extraction method:**
1. Identify the layer structure (e.g., presentation -> business -> data)
2. Extract forbidden dependency directions (e.g., data must not import from presentation)
3. For each forbidden direction, build import check:
   - Identify directory patterns for each layer
   - Use `Grep` to find import/require statements in the restricted layer
   - Check if imported path resolves to the forbidden layer

**Example checks:**
- Files in `src/data/` importing from `src/components/` or `src/pages/`
- Files in `src/utils/` importing from `src/api/`
- Circular dependencies between modules

**Severity:** Architecture boundary violations are CRITICAL.

### From `testing.md`

Testing standards define coverage expectations and test structure rules.

**Extraction method:**
1. Identify the test file naming convention (`.test.ts`, `_test.go`, `test_*.py`)
2. Identify the test directory convention (co-located, `__tests__/`, `tests/`)
3. Extract coverage requirements:
   - Which directories require test files
   - Which file types require tests
   - Minimum assertion counts or test structure rules

**Check method:**
1. Use `Glob` to list all source files in covered directories
2. For each source file, check if corresponding test file exists
3. Calculate coverage ratio: `files_with_tests / total_source_files`

**Severity:** Missing tests for critical paths (API routes, data access, auth) are WARNING. Missing tests for utilities are INFO.

### From `security.md`

Security standards define safe coding practices.

**Extraction method:**
1. Identify each security rule as a pattern to search for:
   - SQL injection: string concatenation in queries (`Grep` for `"SELECT.*" +` or `f"SELECT`)
   - XSS: unescaped output (`Grep` for `innerHTML`, `dangerouslySetInnerHTML`)
   - Secrets: hardcoded keys (`Grep` for common secret variable names with string values)
   - Auth: unprotected routes (`Grep` for route definitions, cross-reference with auth middleware)
2. Build a Grep pattern for each

**Severity:** All security violations are CRITICAL.

### From `error-handling.md`

Error handling standards define how errors must be caught, logged, and propagated.

**Extraction method:**
1. Look for rules about empty catch blocks, swallowed errors, generic error types
2. Build patterns:
   - Empty catch: `catch\s*\([^)]*\)\s*\{\s*\}` (multiline)
   - Console-only error handling: `catch.*console\.(log|warn)` without re-throw
   - Missing error types in function signatures

**Severity:** Empty catch blocks are WARNING. Missing error logging is WARNING. Swallowed errors in critical paths are CRITICAL.

### From `performance.md`

Performance standards define anti-patterns to avoid.

**Extraction method:**
1. Identify explicit anti-patterns:
   - N+1 queries (look for database calls inside loops)
   - Synchronous file I/O in async contexts
   - Missing pagination on list endpoints
   - Unbounded array operations on user data

**Severity:** Performance anti-patterns are INFO unless they affect data integrity (then WARNING).

## Severity Classification

| Severity | Criteria | Weight |
|---|---|---|
| CRITICAL | Security vulnerability, data loss risk, architectural violation, production breakage | 3x |
| WARNING | Code quality degradation, maintainability risk, missing tests, naming violations | 1x |
| INFO | Style preference, minor convention deviation, performance suggestion | 0x (does not affect score) |

## Compliance Score Calculation

```
raw_score = total_rules - (critical_count * 3) - (warning_count * 1)
compliance_score = max(0, (raw_score / total_rules) * 100)
```

**Score interpretation:**
- 95-100%: Excellent compliance
- 85-94%: Good compliance, minor issues
- 70-84%: Needs attention, several violations
- Below 70%: Significant drift from standards

## Handling Non-Testable Rules

Some rules are too subjective or context-dependent for automated checking:
- "Code should be readable"
- "Use meaningful variable names"
- "Follow the principle of least surprise"

**Action:** List these in the Standards Coverage table under "Abstract Rules". Do not attempt to check them. Do not count them in the compliance score denominator.

## Report Best Practices

1. **Group violations by file** when presenting — developers fix file-by-file, not rule-by-rule
2. **Deduplicate** — If the same violation appears 50 times, show 3 examples and state "and 47 more instances"
3. **Order by severity** — Critical first, then Warning, then Info
4. **Include the rule source** — Link each violation back to the specific standard file and section
5. **Cap the report** — If more than 100 violations of a single rule, summarize rather than listing all

## Incremental Auditing

When re-running audit after fixes:
1. Read the previous `.claude/audit-report.md` if it exists
2. Compare findings — report which violations were fixed since last audit
3. Add a "Changes Since Last Audit" section showing improvement or regression
