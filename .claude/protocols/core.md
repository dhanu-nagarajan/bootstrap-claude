# Core Operating Doctrine

## Principles

1. **Evidence-first** — every spec rule must reference actual codebase patterns. No aspirational guidance.
2. **Density over volume** — cut filler, every line must earn its place. Specs were cut from 917 to 373 lines (c5af3ef); guard against bloat creep.
3. **Cross-reference integrity** — paths in CLAUDE.md must match filesystem. Broken refs silently degrade generation quality.
4. **Module isolation** — skills are self-contained (`skills/{name}/`), core is shared (`core/`), registry is data (`registry/`). Never create cross-skill dependencies.
5. **Progressive disclosure** — lite (4 files) proves value fast, standard (14 files) is the daily driver, full (25+ files) is the complete system.

## Research Protocol

Before modifying a spec:
- Read the spec itself
- Read 2-3 files that reference it (check `SKILL.md` files and `master-prompt.md`)
- Map the dependency chain to understand downstream effects

Before modifying `core/`:
- Identify ALL skills that load from the changed file (see cascade map below)
- Test each affected skill after the change

Before adding a skill:
- Read 2 existing `SKILL.md` files to understand the trigger/execution/validation pattern
- Use `.claude/templates/new-skill.md` as the scaffold

## Quality Enforcement

No build/test/lint commands exist. Quality is enforced through:
- Spec clarity (unambiguous rules that Claude can follow)
- Manual testing via `claude --plugin-dir /path/to/bootstrap-claude`
- Cross-reference consistency (grep for stale paths after any file move/rename)

## Cascade Analysis

Changes to these files have the listed blast radius:

| File | Consumers |
|------|-----------|
| `core/analysis/codebase-analyzer.md` | /bootstrap, /roast, /explain, /audit, /doctor |
| `core/validation/reference-validator.md` | /bootstrap, /doctor, /update, /audit |
| `core/fingerprint/fingerprint-spec.md` | /doctor, /update |
| `core/output/progress-format.md` | /bootstrap reporting |
| `core/output/report-format.md` | /audit, /roast, /doctor |
| `skills/bootstrap/references/master-prompt.md` | All generation — the orchestrator |
| `skills/shield/references/shield-spec.md` | /bootstrap (invokes shield), /shield (standalone) |

## Communication Standards

- Lead with conclusions, then evidence
- Structured data over prose (tables, lists, code blocks)
- No sycophancy, no hedging, no filler
