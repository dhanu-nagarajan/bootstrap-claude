# Domain Context

## Problem Statement

Claude Code loses project-specific rules during context window compaction. When sessions run long, the AI summarizes and discards detailed constraints, producing generic output that ignores project conventions. bootstrap-claude generates validated, project-specific `.claude/` engineering systems and protects them from being compacted away.

## Core Concepts

### Skill
A Claude Code trigger (e.g., `/bootstrap`, `/shield`, `/audit`) defined by a `SKILL.md` file with frontmatter specifying name and description. Each skill is self-contained with its own references directory.
See: `skills/*/SKILL.md`

### Generation
The process of analyzing a target codebase and producing `.claude/` files tailored to its language, framework, conventions, and structure. Controlled by tier selection and orchestrated by the master prompt.
See: `skills/bootstrap/references/master-prompt.md`

### Validation
Verifying that every generated reference — file paths, commands, symbols — actually exists in the target codebase. Produces a confidence score per file. Mandatory Step 9 of generation.
See: `core/validation/reference-validator.md`

### Export
Transforming `.claude/` standards into formats understood by other AI coding tools (Cursor, Copilot, Aider, Cline, Agents.md).
See: `skills/export/SKILL.md`, `skills/export/references/formats/`

## External Systems

### GitHub (raw.githubusercontent.com)
Used for version checking and plugin updates. The plugin queries GitHub to compare installed version against latest release.
See: `skills/bootstrap/references/version-check.md`

### Claude Code Plugin System
The host environment that registers and executes skills. Plugin identity defined in `.claude-plugin/plugin.json`. Plugin cache is a filesystem snapshot, not a git repository.
See: `.claude-plugin/plugin.json`, `skills/update/SKILL.md`

## Key Relationships

- `/bootstrap` invokes `/shield` generation as part of its workflow
- `/update` runs `/doctor` internally before applying changes
- `/audit` reads generated `.claude/standards/` to check compliance
- `/onboard` reads generated `.claude/context/` to present walkthroughs
- All skills load shared infrastructure from `core/`
- Registry provides extensible data to generation without code coupling
