# Roast: Detailed Scoring Rubric

This spec provides detailed guidance for the scoring methodology used by the /roast skill.

---

## Scoring Calibration

The scoring system is designed to produce realistic, useful scores:

- **40-60:** Typical early-stage or side project
- **60-75:** Solid production codebase with room for improvement
- **75-85:** Well-maintained project with good practices
- **85-95:** Excellent engineering discipline
- **95-100:** Nearly impossible — reserved for exceptionally well-maintained projects

A brand new `create-next-app` should score ~70 (good defaults, but no custom architecture, no tests beyond boilerplate, no security hardening).

## Dimension Deep Dives

### Architecture Scoring

**What to look for:**

- **Module boundaries** (0-20 points)
  - Clear separation exists (src/handlers, src/services, src/models) → 20
  - Some separation but inconsistent → 10
  - Everything in one directory → 0

- **Dependency direction** (0-20 points)
  - Dependencies flow in one direction (handlers → services → models) → 20
  - Mostly consistent with some violations → 10
  - Circular or no clear direction → 0

- **No circular imports** (0-20 points)
  - Verify with import analysis: no file A importing B importing A → 20
  - One or two circular chains → 10
  - Multiple circular dependency chains → 0

- **Separation of concerns** (0-20 points)
  - Handlers/controllers only route and validate → 20
  - Some business logic in handlers → 10
  - Handlers contain DB queries and business logic → 0

- **Consistent pattern** (0-20 points)
  - Project follows a recognizable architecture pattern consistently → 20
  - Pattern exists but with exceptions → 10
  - No recognizable pattern → 0

### Code Quality Scoring

**What to look for:**

- **Naming consistency** (0-20 points)
  - Grep for naming patterns. If 90%+ consistent → 20
  - If 70-89% consistent → 10
  - If <70% consistent → 0

- **Type safety** (0-20 points)
  - Strict mode enabled + no `any`/`type: ignore` → 20
  - Types present but not strict → 10
  - No type checking → 0

- **Dead code** (0-20 points)
  - No unused exports/functions detected → 20
  - A few unused items → 10
  - Significant dead code → 0

- **Function length** (0-20 points)
  - Average function under 30 lines → 20
  - Average under 50 lines → 10
  - Functions regularly exceed 50 lines → 0

- **DRY** (0-20 points)
  - No significant duplication detected → 20
  - Some repeated patterns → 10
  - Obvious copy-paste code → 0

### Testing Scoring

**What to look for:**

- **Tests exist** (0-20 points)
  - Test directory or test files present → 20
  - No tests → 0

- **Test ratio** (0-20 points)
  - Count test files vs source files
  - Ratio > 0.5 → 20
  - Ratio 0.2-0.5 → 10
  - Ratio < 0.2 → 0

- **Critical paths** (0-20 points)
  - Auth, payment, data mutation code has tests → 20
  - Some critical paths tested → 10
  - Critical paths untested → 0

- **Test quality** (0-20 points)
  - Tests assert specific behavior, not just "doesn't throw" → 20
  - Mixed quality → 10
  - Tests are mostly smoke tests → 0

- **Test health** (0-20 points)
  - No skipped tests, test command works → 20
  - Some skipped tests → 10
  - Tests broken or test command fails → 0

### Anti-Pattern Scoring

**What to count:**

Start at 100, deduct points:

| Pattern | Deduction | Detection Method |
|---------|-----------|-----------------|
| TODO/FIXME/HACK comments | -2 each (cap -30) | Grep for TODO, FIXME, HACK |
| `any` / `type: ignore` / `# noqa` | -5 each | Grep for type escape hatches |
| `unsafe` blocks without SAFETY comment | -5 each | Grep for unsafe (Rust) |
| Files > 500 lines | -3 each | Line count |
| Functions > 100 lines | -3 each | Function analysis |
| Nesting > 4 levels deep | -3 each | Indentation analysis |
| `console.log` / `print()` in production | -2 each (cap -20) | Grep excluding test files |
| Empty catch blocks | -5 each | Grep for catch with empty body |
| Hardcoded credentials/URLs | -10 each | Grep for patterns |

Floor: 0 (score cannot go negative)

## Verdict Templates

Use these as inspiration, adapt to actual findings:

| Score Range | Possible Verdicts |
|-------------|-------------------|
| 90-100 | "Immaculate", "Ship with confidence", "Textbook quality" |
| 80-89 | "Solid bones", "Well-maintained", "Production-ready" |
| 70-79 | "Gets the job done", "Decent foundation", "Room to grow" |
| 60-69 | "Needs attention", "Technical debt accumulating", "Workable but fragile" |
| 50-59 | "Concerning", "Maintenance burden growing", "Refactor soon" |
| 40-49 | "Significant issues", "Risky to modify", "Needs investment" |
| <40 | "Critical shape", "High risk", "Fundamental issues" |

## Report Tone

- Be specific, not vague: "3 functions in `src/api/users.ts` exceed 100 lines" not "some functions are too long"
- Be constructive: every criticism comes with a fix suggestion
- Be fair: acknowledge what's done well before what's not
- Be honest: don't inflate scores to be nice
- Be fun: this is a "roast" — direct language is expected
