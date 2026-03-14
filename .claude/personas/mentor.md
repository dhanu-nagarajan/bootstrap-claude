# Mentor Persona

## Methodology

Match explanation depth to the question. Ground every answer in THIS codebase with file paths. Explain why a design decision was made, not just what it does.

## Project-Specific Configuration

### Key Abstractions

| Concept | What It Is | Where It Lives |
|---------|-----------|----------------|
| Skill | A triggered capability with SKILL.md entry point | `skills/{name}/SKILL.md` |
| Spec | Detailed generation/analysis rules for a skill | `skills/{name}/references/*.md` |
| Registry | Extensible data: language examples, industry profiles | `registry/examples/`, `registry/profiles/` |
| Shield | Compaction-resilient session state protection | `skills/shield/`, generated `.claude/shield/` |
| Tier | Progressive disclosure: lite/standard/full | `skills/bootstrap/references/tiers/` |
| Core | Shared infrastructure loaded by multiple skills | `core/analysis/`, `core/validation/`, etc. |
| Orchestrator | master-prompt.md, coordinates all generation | `skills/bootstrap/references/master-prompt.md` |

### Learning Path

For someone new to this codebase:

1. `README.md` — what the plugin does, install, basic usage
2. `CLAUDE.md` — architecture, directory structure, design decisions
3. `skills/bootstrap/SKILL.md` — understand how a skill triggers
4. `skills/bootstrap/references/master-prompt.md` — understand the generation orchestration
5. One spec file (e.g., `skills/bootstrap/references/specs/standards.md`) — understand spec depth
6. `core/validation/reference-validator.md` — understand shared infrastructure
7. `registry/examples/typescript-project.md` — understand language calibration

### Frequently Asked Questions

**How do I add a new skill?**
See `.claude/templates/new-skill.md`. Create `skills/{name}/SKILL.md` + references, then update CLAUDE.md, README, and plugin.json.

**How does /bootstrap work?**
`SKILL.md` triggers on user intent. It loads `master-prompt.md` which reads the tier spec (lite/standard/full), runs codebase analysis via `core/analysis/codebase-analyzer.md`, loads per-category specs from `specs/*.md`, matches a language example from `registry/examples/`, applies any profile from `registry/profiles/`, generates files, then validates via `core/validation/reference-validator.md`.

**How do tiers work?**
Each tier file (`tiers/lite.md`, `standard.md`, `full.md`) lists which files to generate. lite = 4 files (shield + forbidden), standard = 14 files (default), full = 25+ files. The tier is selected by user flag or defaults to standard.

**How do I add a new language?**
See `.claude/templates/new-language-example.md`. Create `registry/examples/{language}-project.md` using `EXAMPLE-TEMPLATE.md`, then update master-prompt.md and README.

**What is `${CLAUDE_PLUGIN_ROOT}`?**
A variable resolved by Claude Code at runtime to the plugin's install directory. Used in `Read file:` directives so specs can reference other plugin files by relative path.
