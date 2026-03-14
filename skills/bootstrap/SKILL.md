---
name: bootstrap
description: >-
  This skill should be used when the user asks to "bootstrap this project",
  "generate .claude files", "set up engineering standards", "initialize claude
  for this project", "create protocols", "bootstrap claude", or invokes
  /bootstrap. It analyzes the current codebase and generates a tiered
  .claude/ engineering system — standards, shield protection, checklists,
  context, protocols, templates, and personas — all tailored to the specific
  project. Supports tiers (--lite, --standard, --full), org profiles,
  custom specs, and validates all generated references against the actual
  codebase.
---

# Bootstrap: .claude/ Engineering System Generator v3

Generate a `.claude/` directory structure that shapes how Claude Code operates in the current project. Every generated file is tailored to the specific codebase — no generic filler. Includes compaction-resilient session protection via the Shield system.

**Quality bar:** Every file must reflect the standards of a principal engineer at Anthropic, Apple, or Google. Every sentence specific, actionable, grounded in what is discovered about THIS project.

## Tiers

Generation is tiered for progressive disclosure:

| Tier | Files | Time | What You Get |
|------|-------|------|-------------|
| `--lite` | 4 | ~30s | Shield + forbidden patterns. Proves value immediately. |
| `--standard` | 14 | ~2min | + standards, checklists, context. Daily driver. **DEFAULT** |
| `--full` | 25+ | ~5min | + protocols, templates, personas. Complete system. |

## Flags

| Flag | Effect |
|------|--------|
| `--lite` | Generate minimal protection (4 files) |
| `--standard` | Generate daily driver set (14 files) — DEFAULT |
| `--full` | Generate complete engineering system (25+ files) |
| `--dry-run` | Preview what would be generated without writing files |
| `--verbose` | Show analysis reasoning and decision process |
| `--only <list>` | Generate only specified categories (comma-separated) |
| `--exclude <list>` | Skip specified categories (comma-separated) |
| `--profile <name>` | Use industry profile (overrides config file) |

**Categories for `--only` and `--exclude`:** standards, shield, checklists, context, protocols, templates, personas

## Execution

### Step 0: Version Check

Before starting, check if this plugin is up to date:

```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/version-check.md
```

Follow the version check protocol. If the plugin is outdated, show the update notice before proceeding. Do not block — continue with bootstrap regardless.

### Step 1: Parse Flags

Determine the generation tier and any flags from the user's invocation:

- No flags → standard tier
- `--lite` → lite tier
- `--full` → full tier
- `--dry-run` → generate to memory, show preview, don't write files
- `--only X,Y` → generate only listed categories (any tier)
- `--exclude X` → skip listed categories
- `--profile P` → use specified profile (overrides .bootstrap-config.yaml)
- `--verbose` → show detailed analysis reasoning

Also check `.bootstrap-config.yaml` for tier, profile, and other settings.

### Step 2: Deep Project Analysis & Generation

Read the master orchestrator prompt which coordinates all analysis and generation:

```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/master-prompt.md
```

That file coordinates:
1. Loading the tier spec
2. Deep codebase analysis using shared protocols
3. Language detection and example loading from registry
4. Profile loading (built-in or custom)
5. Custom spec loading
6. File generation per tier spec
7. CLAUDE.md update with recovery protocol
8. Auto-export (if configured)
9. Reference validation with confidence scoring
10. Progress reporting

Follow it completely.

### Step 3: Post-Generation Guidance

After the system is generated and validated, inform the user about the additional skills available:

- **`/shield`** — Refresh compaction-resilient session protection
- **`/audit`** — Validate codebase compliance against the generated standards
- **`/doctor`** — Check for drift between `.claude/` files and codebase reality
- **`/update`** — Incrementally refresh `.claude/` files when the codebase evolves
- **`/export`** — Export standards to other AI tools (Cursor, Copilot, Aider, Cline)
- **`/onboard`** — Interactive codebase walkthrough for new team members
- **`/roast`** — Get a scored codebase quality report
- **`/explain`** — Generate an architecture narrative document

If on lite or standard tier, mention what the next tier up would add.

## Quality Principles

1. **Specific over generic** — Reference actual code, types, functions, config
2. **Evidence-based** — Every rule grounded in patterns found in the codebase
3. **Actionable** — Every statement tells what to DO
4. **Minimal** — No filler, no obvious advice
5. **Opinionated** — "Do X" not "Consider doing X"
6. **Grounded** — If no evidence found in codebase, don't invent it
7. **Validated** — Every reference verified against the actual codebase
8. **Progressive** — Start small, earn trust, offer more

**The test:** Would a principal engineer find this useful, or delete it as noise?

## Additional Resources

### Reference Files

The generation system is modular:
- **`references/master-prompt.md`** — Master orchestrator coordinating all generation
- **`references/tiers/`** — Tier specifications (lite, standard, full)
- **`references/specs/*.md`** — Per-category generation specs (protocols, standards, templates, checklists, personas, context)

### Registry (extensible)
- **`registry/examples/*.md`** — Language-specific reference output (TypeScript, Python, Rust, Go, Java)
- **`registry/profiles/*.md`** — Industry/org profile overlays (fintech, healthcare, startup, open-source)

### Core Infrastructure (shared across skills)
- **`core/analysis/codebase-analyzer.md`** — Language detection, convention extraction, structure mapping
- **`core/validation/reference-validator.md`** — Reference validation + confidence scoring
- **`core/fingerprint/`** — Codebase fingerprinting
- **`core/output/`** — Progress and report formatting
