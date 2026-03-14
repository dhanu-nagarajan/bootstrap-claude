# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

bootstrap-claude is an **enterprise-grade Claude Code plugin** providing 7 integrated skills that generate, validate, maintain, and export a complete `.claude/` engineering system. The killer feature is compaction-resilient session protection (`/shield`) — solving Claude Code's #1 user complaint.

## Repository Structure

```
.claude-plugin/
  plugin.json              # Plugin identity (name, version, description)
  marketplace.json         # Public marketplace registration

skills/
  bootstrap/               # Core generation skill
    SKILL.md               # Entry point — triggers, execution flow
    references/
      master-prompt.md     # Orchestrator — coordinates all generation
      specs/               # Per-category generation specs
        protocols.md       #   Protocol generation rules
        standards.md       #   Standards generation rules (with validation requirements)
        templates.md       #   Template generation rules
        checklists.md      #   Checklist generation rules
        personas.md        #   Persona generation rules (with project-tailoring)
        context.md         #   Context generation rules (with traceability)
      examples/            # Language-specific reference output
        typescript-project.md
        python-project.md
        rust-project.md
      profiles/            # Industry/org overlays
        fintech.md
        healthcare.md
        startup.md
        open-source.md

  shield/                  # Compaction-resilient session protection
    SKILL.md
    references/
      shield-spec.md       # Generation spec for shield files
      recovery-template.md # CLAUDE.md injection template

  audit/                   # Standards compliance checking
    SKILL.md
    references/
      audit-spec.md        # Rule extraction and scoring spec

  doctor/                  # Drift detection
    SKILL.md
    references/
      doctor-spec.md       # Reference validation and fingerprinting spec

  update/                  # Incremental maintenance
    SKILL.md
    references/
      update-spec.md       # Merge strategy and state management spec

  export/                  # Cross-tool export
    SKILL.md
    references/
      export-spec.md       # General transformation principles
      formats/
        cursor.md          # .cursor/rules/*.mdc format
        copilot.md         # .github/copilot-instructions.md format
        aider.md           # .aider.conventions.md format
        cline.md           # .clinerules format
        agents.md          # AGENTS.md format

  onboard/                 # Interactive codebase walkthrough
    SKILL.md
    references/
      onboard-spec.md     # Walkthrough structure and adaptation spec
```

## Architecture

### Skill Dependency Graph
```
/bootstrap ──→ generates .claude/ system
    ├── loads specs/*.md for each category
    ├── loads examples/*.md for language calibration
    ├── loads profiles/*.md for org overlays
    ├── invokes shield generation (shield-spec.md)
    └── runs validation (Step 7)

/shield ──→ standalone or invoked by bootstrap
/audit ──→ reads .claude/standards/, scans codebase
/doctor ──→ reads all .claude/ files, validates references
/update ──→ runs doctor internally, applies surgical updates
/export ──→ reads .claude/, transforms to target formats
/onboard ──→ reads .claude/context/, presents walkthrough
```

### Key Design Decisions

- **master-prompt.md is the orchestrator** — it coordinates loading modular specs, not a monolithic generation spec. Each spec in `specs/` is self-contained.
- **Shield is generated during bootstrap** — not a separate step users must remember.
- **Validation is mandatory** — Step 7 of master-prompt.md verifies every reference. This is the #1 differentiator from v1.
- **Profiles layer on top** — they add rules, never replace base generation.
- **`${CLAUDE_PLUGIN_ROOT}`** resolves to the plugin install directory at runtime.

## Working on This Project

No build, lint, or test commands — all files are Markdown/JSON. Impact hierarchy:

1. **`specs/*.md`** — Highest impact. Changes affect generated output quality for ALL target projects.
2. **`master-prompt.md`** — Controls generation flow and validation. Changes affect what gets generated and in what order.
3. **Individual SKILL.md files** — Control trigger conditions and execution flow for each skill.
4. **`examples/*.md`** — Calibrate output quality for specific languages.
5. **`profiles/*.md`** — Add industry-specific rules.
6. **`formats/*.md`** — Control export output for specific tools.
7. **`plugin.json`** — Bump `version` for releases.

## Commit Style

Imperative mood, concise (e.g., "Add /shield skill for compaction resilience", "Fix validation confidence scoring").
