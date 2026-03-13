---
name: bootstrap
description: >-
  This skill should be used when the user asks to "bootstrap this project",
  "generate .claude files", "set up engineering standards", "initialize claude
  for this project", "create protocols", "bootstrap claude", or invokes
  /bootstrap. It analyzes the current codebase and generates a complete
  .claude/ engineering system — protocols, standards, templates, checklists,
  personas, and domain context — all tailored to the specific project.
---

# Bootstrap: .claude/ Engineering System Generator

Generate a complete `.claude/` directory structure that shapes how Claude Code operates in the current project. Every generated file is tailored to the specific codebase — no generic filler.

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

If a `CLAUDE.md` already exists, preserve its project-specific content and integrate it into the generated system. Do not overwrite user-written sections.

### Step 2: Generate Directory Structure

Read the full specification from the master prompt:

```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/master-prompt.md
```

That file contains the complete content specifications for every directory and file. Follow it exactly, tailoring all content to the project analysis from Step 1.

**Directory tree to generate:**

```
.claude/
├── protocols/        — core.md, request.md, refresh.md, retro.md
├── standards/        — architecture.md, language.md, components.md (if UI),
│                       testing.md, error-handling.md, performance.md,
│                       security.md, forbidden.md
├── templates/        — 2-4 project-specific scaffolding templates
├── checklists/       — pre-commit.md, new-module.md, pr-review.md, incident.md
├── personas/         — architect.md, reviewer.md, optimizer.md, debugger.md, mentor.md
└── context/          — domain.md, decisions.md, glossary.md
```

**Adapt to the project:**
- Skip `standards/components.md` if no UI layer exists
- Skip UI-related persona details for backend-only projects
- Templates should match what this project actually builds repeatedly
- All commands in checklists must be the project's actual commands
- ADRs in `context/decisions.md` inferred from actual codebase patterns

### Step 3: Update CLAUDE.md

After generating all `.claude/` files, update the project's `CLAUDE.md` with the complete routing table. The routing table maps task types to protocol files, personas to trigger phrases, and lists all standards, checklists, templates, and context files.

If `CLAUDE.md` already has project-specific content (overview, commands, architecture), preserve it. Add the operational system routing table above the existing content.

### Step 4: Verify

1. Run `find .claude -type f | sort` to list all created files
2. Verify CLAUDE.md routing table references every generated file
3. Spot-check 2-3 files for project-specific content (not generic advice)
4. Verify all commands in checklists actually exist in `package.json` / `Makefile` / equivalent
5. Report summary: what was created, file count, any gaps needing manual input

## Quality Principles

1. **Specific over generic** — Reference actual code, types, functions, config
2. **Evidence-based** — Every rule grounded in patterns found in the codebase
3. **Actionable** — Every statement tells what to DO
4. **Minimal** — No filler, no obvious advice
5. **Opinionated** — "Do X" not "Consider doing X"
6. **Grounded** — If no evidence found in codebase, don't invent it

**The test:** Would a principal engineer find this useful, or delete it as noise?

## Additional Resources

### Reference Files

The complete file content specifications are in:
- **`references/master-prompt.md`** — Full specification for every generated file including protocols, standards, templates, checklists, personas, and context. This is the authoritative source for what each file must contain.
