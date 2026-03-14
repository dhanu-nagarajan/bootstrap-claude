---
name: bootstrap
description: >-
  This skill should be used when the user asks to "bootstrap this project",
  "generate .claude files", "set up engineering standards", "initialize claude
  for this project", "create protocols", "bootstrap claude", or invokes
  /bootstrap. It analyzes the current codebase and generates a complete
  .claude/ engineering system — protocols, standards, templates, checklists,
  personas, domain context, and compaction-resilient session protection — all
  tailored to the specific project. Supports org profiles and validates all
  generated references against the actual codebase.
---

# Bootstrap: .claude/ Engineering System Generator v2

Generate a complete `.claude/` directory structure that shapes how Claude Code operates in the current project. Every generated file is tailored to the specific codebase — no generic filler. Includes compaction-resilient session protection via the Shield system.

**Quality bar:** Every file must reflect the standards of a principal engineer at Anthropic, Apple, or Google. Every sentence specific, actionable, grounded in what is discovered about THIS project.

## Execution

### Step 1: Deep Project Analysis

Before generating anything, perform comprehensive analysis using subagents for speed. Discover:

1. **Package manifest** — `package.json`, `Cargo.toml`, `go.mod`, `requirements.txt`, `pyproject.toml`, or equivalent
2. **Config files** — Language config, linters, formatters, build tools, CI/CD, Docker
3. **Directory structure** — Map the architecture via `ls` on root and key subdirectories
4. **Entry points** — Main application entry, routing, API surface
5. **Existing conventions** — Read 3-5 representative source files for naming, structure, error handling, testing patterns
6. **Existing rules** — Check for `CLAUDE.md`, `.cursorrules`, `.cursor/rules/`, `.github/copilot-instructions.md`, `AGENTS.md`, `CONTRIBUTING.md`
7. **Git history** — `git log --oneline -20` for development patterns and commit style
8. **Test infrastructure** — Framework, file conventions, how to run
9. **Dependencies** — Key libraries and their architectural roles
10. **Bootstrap config** — Check for `.bootstrap-config.yaml` for org-level overrides

If a `CLAUDE.md` already exists, preserve its project-specific content and integrate it into the generated system. Do not overwrite user-written sections.

### Step 2: Generate Full System

Read the master orchestrator prompt which coordinates all generation:

```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/master-prompt.md
```

That file coordinates loading per-category specs, language examples, org profiles, shield generation, CLAUDE.md updates, and validation. Follow it completely.

### Step 3: Post-Generation Guidance

After the system is generated and validated, inform the user about the additional skills available:

- **`/shield`** — Activate compaction-resilient session protection (already generated during bootstrap, but can be refreshed)
- **`/audit`** — Validate codebase compliance against the generated standards
- **`/doctor`** — Check for drift between `.claude/` files and codebase reality
- **`/update`** — Incrementally refresh `.claude/` files when the codebase evolves
- **`/export`** — Export standards to other AI tools (Cursor, Copilot, Aider, Cline)
- **`/onboard`** — Interactive codebase walkthrough for new team members

## Quality Principles

1. **Specific over generic** — Reference actual code, types, functions, config
2. **Evidence-based** — Every rule grounded in patterns found in the codebase
3. **Actionable** — Every statement tells what to DO
4. **Minimal** — No filler, no obvious advice
5. **Opinionated** — "Do X" not "Consider doing X"
6. **Grounded** — If no evidence found in codebase, don't invent it
7. **Validated** — Every reference verified against the actual codebase

**The test:** Would a principal engineer find this useful, or delete it as noise?

## Additional Resources

### Reference Files

The generation system is modular:
- **`references/master-prompt.md`** — Master orchestrator coordinating all generation
- **`references/specs/*.md`** — Per-category generation specs (protocols, standards, templates, checklists, personas, context)
- **`references/examples/*.md`** — Language-specific reference output (TypeScript, Python, Rust)
- **`references/profiles/*.md`** — Industry/org profile overlays (fintech, healthcare, startup, open-source)
