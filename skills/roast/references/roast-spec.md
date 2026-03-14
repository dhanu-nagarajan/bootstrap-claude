# Roast: Scoring Calibration & Reference

Dimension scoring rules live in the SKILL.md. This spec provides calibration, anti-pattern deductions, verdict templates, and tone guidance.

## Calibration Benchmarks

- **40-60:** Typical early-stage or side project
- **60-75:** Solid production codebase with room for improvement
- **75-85:** Well-maintained project with good practices
- **85-95:** Excellent engineering discipline
- **95-100:** Nearly impossible — reserved for exceptional projects

A brand new `create-next-app` should score ~70 (good defaults, but no custom architecture, no tests beyond boilerplate, no security hardening).

## Anti-Pattern Deduction Table

Start at 100, deduct:

| Pattern | Deduction | Detection |
|---------|-----------|-----------|
| TODO/FIXME/HACK comments | -2 each (cap -30) | Grep |
| `any` / `type: ignore` / `# noqa` | -5 each | Grep |
| `unsafe` without SAFETY comment | -5 each | Grep (Rust) |
| Files > 500 lines | -3 each | Line count |
| Functions > 100 lines | -3 each | Function analysis |
| Nesting > 4 levels deep | -3 each | Indentation analysis |
| `console.log` / `print()` in prod | -2 each (cap -20) | Grep excluding tests |
| Empty catch blocks | -5 each | Grep |
| Hardcoded credentials/URLs | -10 each | Grep |

Floor: 0 (score cannot go negative).

## Verdict Templates

| Score | Possible Verdicts |
|-------|-------------------|
| 90-100 | "Immaculate", "Ship with confidence", "Textbook quality" |
| 80-89 | "Solid bones", "Well-maintained", "Production-ready" |
| 70-79 | "Gets the job done", "Decent foundation", "Room to grow" |
| 60-69 | "Needs attention", "Technical debt accumulating", "Workable but fragile" |
| 50-59 | "Concerning", "Maintenance burden growing", "Refactor soon" |
| 40-49 | "Significant issues", "Risky to modify", "Needs investment" |
| <40 | "Critical shape", "High risk", "Fundamental issues" |

## Tone

- **Be specific:** "3 functions in `src/api/users.ts` exceed 100 lines" not "some functions are too long"
- **Be constructive:** every criticism comes with a fix suggestion
- **Be honest:** don't inflate scores to be nice
- **Be fun:** this is a "roast" — direct language is expected
