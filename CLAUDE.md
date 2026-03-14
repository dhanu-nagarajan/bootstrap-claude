# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

bootstrap-claude is a **Claude Code plugin** providing 9 integrated skills that generate, validate, maintain, and export a complete `.claude/` engineering system. The killer feature is compaction-resilient session protection (`/shield`). v3 introduces tiered generation, a registry system for extensibility, and two new skills (`/roast`, `/explain`).

## Repository Structure

```
.claude-plugin/
  plugin.json              # Plugin identity (name: bootstrap-claude, version: 3.0.0)
  marketplace.json         # Public marketplace registration
  config.schema.json       # JSON Schema for .bootstrap-config.yaml validation

core/                      # Shared infrastructure (used by ALL skills)
  analysis/
    codebase-analyzer.md   # Unified analysis protocol
    language-detection.md  # Language & framework detection rules
    convention-extraction.md # Pattern extraction from source files
  validation/
    reference-validator.md # Path/command/symbol verification
    confidence-scorer.md   # Scoring methodology and thresholds
  fingerprint/
    fingerprint-spec.md    # SHA-256 codebase hashing for drift detection
  output/
    progress-format.md     # Standardized CLI progress output
    report-format.md       # Standardized report formatting

skills/
  bootstrap/               # Core generation skill
    SKILL.md               # Entry point — triggers, flags, tiers
    references/
      master-prompt.md     # Orchestrator v3 — tier-aware, registry-based
      version-check.md     # Plugin update check protocol
      tiers/               # Generation tier specifications
        lite.md            #   4 files: shield + forbidden
        standard.md        #   14 files: + standards, checklists, context (DEFAULT)
        full.md            #   25+ files: + protocols, templates, personas
      specs/               # Per-category generation specs
        protocols.md       #   Protocol generation rules
        standards.md       #   Standards generation rules (with validation)
        templates.md       #   Template generation rules
        checklists.md      #   Checklist generation rules
        personas.md        #   Persona generation rules
        context.md         #   Context generation rules

  shield/                  # Compaction-resilient session protection
    SKILL.md
    references/
      shield-spec.md       # Generation spec for shield files
      recovery-template.md # CLAUDE.md injection template
      auto-refresh.md      # Milestone-based auto-update protocol

  audit/                   # Standards compliance checking
    SKILL.md
    references/
      audit-spec.md        # Rule extraction and scoring spec
      ci-mode.md           # CI/CD JSON output and exit codes
      badge-spec.md        # Compliance badge generation

  doctor/                  # Drift detection
    SKILL.md
    references/
      doctor-spec.md       # Reference validation and fingerprinting spec

  update/                  # Incremental maintenance + plugin self-update
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
        FORMAT-TEMPLATE.md # Guide for adding custom export formats

  onboard/                 # Interactive codebase walkthrough
    SKILL.md
    references/
      onboard-spec.md      # Walkthrough structure and adaptation spec

  roast/                   # NEW: Codebase quality report
    SKILL.md
    references/
      roast-spec.md        # Scoring rubric and calibration

  explain/                 # NEW: Architecture narrative generator
    SKILL.md
    references/
      explain-spec.md      # Diagram style, voice, project type handling

registry/                  # Extensible resource system
  registry-spec.md         # How the registry works (resolution order, custom resources)
  examples/                # Language-specific calibration output
    typescript-project.md
    python-project.md
    rust-project.md
    go-project.md          # NEW
    java-project.md        # NEW
    EXAMPLE-TEMPLATE.md    # Guide for adding new languages
  profiles/                # Industry/org overlays
    fintech.md
    healthcare.md
    startup.md
    open-source.md
    PROFILE-TEMPLATE.md    # Guide for creating custom profiles

actions/                   # CI/CD integrations
  audit/
    action.yml             # GitHub Action for PR audit
    README.md              # Action usage documentation

showcase/                  # Real generated output for evaluation
  README.md
  nextjs-app/README.md     # Next.js showcase (standard tier)
  fastapi-service/README.md # FastAPI showcase (standard tier)
  rust-cli/README.md       # Rust CLI showcase (lite tier)
```

## Architecture

### Skill Dependency Graph
```
/bootstrap ──→ generates .claude/ system
    ├── reads tier spec (tiers/lite|standard|full.md)
    ├── loads core/analysis/* for codebase analysis
    ├── loads specs/*.md for each category
    ├── loads registry/examples/*.md for language calibration
    ├── loads registry/profiles/*.md for org overlays
    ├── invokes shield generation (shield-spec.md)
    ├── loads core/validation/* for reference verification
    └── runs validation (Step 9)

/shield ──→ standalone or invoked by bootstrap
    └── auto-refresh at milestones (if configured)
/audit ──→ reads .claude/standards/, scans codebase
    ├── CI mode with JSON output + exit codes
    └── generates compliance badge
/doctor ──→ reads all .claude/ files, validates references
/update ──→ runs doctor internally, applies surgical updates
/export ──→ reads .claude/, transforms to target formats
/onboard ──→ reads .claude/context/, presents walkthrough
/roast ──→ analyzes codebase, scores 6 dimensions, generates report
/explain ──→ traces request flow, generates architecture narrative
```

### Key Design Decisions

- **Tiered generation** — lite (4 files) / standard (14, default) / full (25+). Progressive disclosure lowers barrier.
- **Registry system** — Examples, profiles, and export formats are extensible without forking. Resolution: project-local → built-in → community.
- **Shared core infrastructure** — `core/` provides analysis, validation, fingerprinting, and output protocols used by all skills. Eliminates duplication.
- **master-prompt.md is the orchestrator** — coordinates loading modular specs via tier specs, not monolithic generation.
- **Shield is generated during bootstrap** — not a separate step users must remember. Auto-refreshes at milestones.
- **Validation is mandatory** — Step 9 of master-prompt.md verifies every reference. Confidence scoring per file.
- **Profiles layer on top** — they add rules, never replace base generation.
- **`${CLAUDE_PLUGIN_ROOT}`** resolves to the plugin install directory at runtime.
- **`/update` is dual-mode** — detects "update plugin" vs "update standards" from user intent.
- **CI integration** — `/audit` supports JSON output, exit codes, and compliance badge generation.

### Layer Architecture
```
┌─────────────────────────────────────────────────┐
│                 User Interface                   │
│          SKILL.md files (triggers + flags)       │
├────────────┬────────────┬───────────────────────┤
│ Generation  │Maintenance │    Distribution       │
│ /bootstrap  │/audit      │    /export            │
│ /shield     │/doctor     │    /onboard           │
│ /roast      │/update     │    /explain           │
├────────────┴────────────┴───────────────────────┤
│              Core Services (core/)               │
│  Analysis │ Validation │ Fingerprint │ Output    │
├──────────────────────────────────────────────────┤
│           Registry (registry/, extensible)        │
│  Examples │ Profiles │ Specs │ Formats            │
└──────────────────────────────────────────────────┘
```

## Working on This Project

No build, lint, or test commands — all files are Markdown/JSON/YAML. Impact hierarchy:

1. **`core/`** — Highest impact. Changes affect ALL skills. Test thoroughly.
2. **`skills/bootstrap/references/specs/*.md`** — Affects generated output quality for ALL target projects.
3. **`skills/bootstrap/references/master-prompt.md`** — Controls generation flow. Changes affect what gets generated and in what order.
4. **`skills/bootstrap/references/tiers/*.md`** — Controls which files each tier generates.
5. **Individual `SKILL.md` files** — Control trigger conditions and execution flow for each skill.
6. **`registry/examples/*.md`** — Calibrate output quality for specific languages.
7. **`registry/profiles/*.md`** — Add industry-specific rules.
8. **`skills/export/references/formats/*.md`** — Control export output for specific tools.
9. **`plugin.json`** — Bump `version` for releases.

## Commit Style

Imperative mood, concise (e.g., "Add Go language example", "Fix validation confidence scoring", "Add CI mode to audit skill").
