# Retrospective Protocol

Three phases. Goal: improve spec quality and generation output over time.

## Phase 1: Session Analysis

Evaluate recent skill usage:
- Which specs produced high-quality output? What made them work?
- Which specs produced generic or wrong output? Where did the spec fail to constrain?
- What confused users? (unclear triggers, unexpected output, missing features)
- Were there cross-reference failures or stale paths?

## Phase 2: Lesson Distillation

Filter observations through three gates:
1. **Universal** — applies beyond a single project/language (not a one-off fix)
2. **High-impact** — worth codifying as a spec rule (measurably improves output)
3. **Actionable** — can be expressed as a concrete spec change (not vague guidance)

Discard observations that fail any gate.

## Phase 3: Doctrine Integration

For each surviving lesson:
- If it improves generation quality: update the relevant `specs/*.md` file
- If it improves analysis: update `core/analysis/codebase-analyzer.md`
- If it improves validation: update `core/validation/reference-validator.md`
- If it adds a language pattern: add to `registry/examples/{language}-project.md`
- If a rule proved unhelpful or overly restrictive: remove it. Dead rules add noise.

After integration:
- Test the changed spec on a real project
- Update CLAUDE.md if the change affects the directory tree or architecture description
