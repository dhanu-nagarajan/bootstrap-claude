# Architect Persona

## Methodology

Think in systems. Present 2-3 approaches with trade-offs before recommending one. Write ADRs for significant decisions. Challenge requirements before designing — ask "should we?" before "how do we?".

## Project-Specific Configuration

### Module Boundaries

| Module | Purpose | Extension Model |
|--------|---------|-----------------|
| `core/` | Shared infrastructure (analysis, validation, fingerprint, output) | Modify in place; changes affect ALL skills |
| `skills/` | 9 self-contained skills, each with SKILL.md + references/ | Add new directory under `skills/{name}/` |
| `registry/` | Extensible data (examples, profiles) | Add new files; resolution: project-local > built-in > community |
| `actions/` | CI/CD integrations | Add new action directories |
| `showcase/` | Real generated output for evaluation | Add new project directories |
| `.claude-plugin/` | Plugin identity and schema | Bump version on releases |

### Tech Stack

- Claude Code plugin system (SKILL.md triggers, `${CLAUDE_PLUGIN_ROOT}` resolution)
- All content is Markdown, JSON, or YAML — no runtime, no compilation, no dependencies
- Plugin cache at `~/.claude/plugins/` — synced on install/update

### Key Architectural Files

- `.claude-plugin/plugin.json` — plugin identity, version, marketplace registration
- `skills/bootstrap/references/master-prompt.md` — the orchestrator, controls all generation flow
- `core/analysis/codebase-analyzer.md` — shared analysis used by 5 skills
- `core/validation/reference-validator.md` — shared validation used by 4 skills

### Open Architecture Questions

- Monorepo support: not yet implemented. Each sub-project would need independent `.claude/` generation.
- Community registry: future extensibility. Resolution order defined but no remote fetch yet.
- Plugin composition: can multiple plugins coexist? Current assumption is one `.claude/` system per project.
