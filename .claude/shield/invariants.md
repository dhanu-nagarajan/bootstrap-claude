# Shield Invariants

> Top rules for bootstrap-claude plugin development. Re-read after every compaction.

## Critical Commands

NONE. This is a pure Markdown/JSON/YAML project. No build, test, or lint commands.

## Hard Constraints

1. Every generated rule must reference patterns ACTUALLY FOUND in the target codebase — never invent generic advice
2. Every file path, command, and symbol reference must be verified before output (`core/validation/reference-validator.md`)
3. Never overwrite user-edited sections marked with `<!-- user-edited -->` markers
4. `shield/invariants.md` must stay under 2000 tokens — longer defeats the purpose
5. `recovery-protocol.md` must be under 50 lines — must read in 30 seconds

## Important Rules

6. Commit style: imperative mood, concise (e.g., "Add Go language example")
7. Impact hierarchy: `core/` changes affect ALL skills — test thoroughly
8. `master-prompt.md` is the orchestrator — it coordinates loading modular specs, not monolithic generation
9. Profiles layer on top — they add rules, never replace base generation
10. Plugin cache is NOT a git repo — never use `git pull` on `${CLAUDE_PLUGIN_ROOT}`
11. Tier system: lite (4 files) / standard (14) / full (25+) — respect tier boundaries
12. Registry resolution order: project-local -> built-in -> community

## Forbidden Patterns

- Generic advice not grounded in actual codebase analysis
- Unverified references (paths, commands, symbols)
- Exceeding token budgets on shield files
- `git pull` on plugin cache directory
- Removing forbidden/security rules during `/update`
- Blocking user workflow on version check failures
- Referencing `${CLAUDE_PLUGIN_ROOT}` in generated user-facing output

## Architecture Boundaries

- `core/` provides shared infrastructure — never imports from `skills/` or `registry/`
- `skills/` are self-contained — each has `SKILL.md` + `references/`
- `registry/` is extensible data — never imports from anything
- Generated `.claude/` output references user's project paths, not plugin-internal paths

## Impact Hierarchy (change risk, highest first)

1. `core/` — affects ALL skills
2. `skills/bootstrap/references/specs/*.md` — affects generated output quality
3. `skills/bootstrap/references/master-prompt.md` — controls generation flow
4. `skills/bootstrap/references/tiers/*.md` — controls tier file lists
5. Individual `SKILL.md` files — control trigger and execution per skill
6. `registry/examples/*.md` — calibrate language-specific output
7. `registry/profiles/*.md` — add industry rules
8. `skills/export/references/formats/*.md` — control export output
9. `.claude-plugin/plugin.json` — bump version for releases
