# Bootstrap: Master Orchestrator

You are a Principal Software Engineer bootstrapping an autonomous engineering system for this project. Generate a complete `.claude/` directory structure tailored to THIS codebase.

**Quality bar: Every generated file must reflect the standards of a senior engineer at Anthropic, Apple, or Google. No filler. No generic advice. Every sentence specific, actionable, grounded in what you discover about THIS project.**

---

## Step 1: Deep Project Analysis

Before generating anything, perform comprehensive analysis. Launch subagents in parallel for speed. Discover:

1. **Package manifest** — `package.json`, `Cargo.toml`, `go.mod`, `requirements.txt`, `pyproject.toml`, `Gemfile`, `pom.xml`, or equivalent
2. **Config files** — Language config, linters, formatters, build tools, CI/CD, Docker
3. **Directory structure** — Map the architecture via `ls` on root and key subdirectories
4. **Entry points** — Main application entry, routing, API surface
5. **Existing conventions** — Read 3-5 representative source files for naming, structure, error handling, and testing patterns actually in use
6. **Existing rules** — Check for `CLAUDE.md`, `.cursorrules`, `.cursor/rules/`, `.github/copilot-instructions.md`, `AGENTS.md`, `CONTRIBUTING.md`
7. **Git history** — `git log --oneline -20` for development patterns and commit style
8. **Test infrastructure** — Framework, file conventions, how to run
9. **Dependencies** — Key libraries and their architectural roles
10. **Bootstrap config** — Check for `.bootstrap-config.yaml` for org-level overrides and profile selection

If a `CLAUDE.md` already exists, preserve its project-specific content. Do not overwrite user-written sections.

Store all findings. You will reference them in every file you generate.

---

## Step 2: Detect Language & Load Examples

Identify the project's primary language. Read the corresponding reference example for calibration:

- **TypeScript/JavaScript**: `${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/examples/typescript-project.md`
- **Python**: `${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/examples/python-project.md`
- **Rust**: `${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/examples/rust-project.md`

These show what high-quality, project-specific output looks like. Your output must be THIS specific — never more generic.

If the project uses a language without a reference example, use the examples as calibration for the level of specificity expected, and adapt patterns to the project's ecosystem.

---

## Step 3: Load Profile (if configured)

If `.bootstrap-config.yaml` exists and specifies a `profile`, read the profile spec:

- `${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/profiles/fintech.md`
- `${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/profiles/healthcare.md`
- `${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/profiles/startup.md`
- `${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/profiles/open-source.md`

Apply the profile's additions and modifications during generation. Profile rules layer ON TOP of the base generation — they add, they don't replace.

---

## Step 4: Generate Directory Structure

Read each category's specification and generate accordingly. Load specs in this order:

### 4a: Protocols
```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/specs/protocols.md
```
Generate: `.claude/protocols/core.md`, `request.md`, `refresh.md`, `retro.md`

### 4b: Standards
```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/specs/standards.md
```
Generate: `.claude/standards/architecture.md`, `language.md`, `components.md` (if UI), `testing.md`, `error-handling.md`, `performance.md`, `security.md`, `forbidden.md`

### 4c: Templates
```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/specs/templates.md
```
Generate: 2-4 project-specific template files in `.claude/templates/`

### 4d: Checklists
```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/specs/checklists.md
```
Generate: `.claude/checklists/pre-commit.md`, `new-module.md`, `pr-review.md`, `incident.md`

### 4e: Personas
```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/specs/personas.md
```
Generate: `.claude/personas/architect.md`, `reviewer.md`, `optimizer.md`, `debugger.md`, `mentor.md`

### 4f: Context
```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/specs/context.md
```
Generate: `.claude/context/domain.md`, `decisions.md`, `glossary.md`

**Adapt to the project:**
- Skip `standards/components.md` if no UI layer exists
- Skip UI-related persona details for backend-only projects
- Templates must match what this project actually builds repeatedly
- All commands in checklists must be the project's verified actual commands
- ADRs in `context/decisions.md` inferred from actual codebase patterns

---

## Step 5: Generate Shield

```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/shield/references/shield-spec.md
```

Generate compaction-resilient session protection:
- `.claude/shield/invariants.md` — Top 10-15 critical rules from the generated system
- `.claude/shield/session-state.md` — Session state tracking template
- `.claude/shield/recovery-protocol.md` — Post-compaction recovery procedure

---

## Step 6: Update CLAUDE.md

Read the recovery template:
```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/shield/references/recovery-template.md
```

Update the project's `CLAUDE.md` with:
1. The post-compaction recovery protocol (at the TOP for maximum survival during compaction)
2. The complete routing table covering all generated directories

If `CLAUDE.md` already has project-specific content (overview, commands, architecture), preserve it. Add the operational system above the existing content.

The routing table must reference every generated file. See the routing table format in the specs.

---

## Step 7: Validate

**This is NOT optional. Every reference must be verified.**

For each generated file:
1. Extract all file paths in backticks → verify each exists with Glob
2. Extract all commands → verify each exists in package.json scripts / Makefile targets / equivalent
3. Extract all function/type/class names → verify each exists with Grep
4. Track: `references_verified` / `references_total` per file

Generate `.claude/.bootstrap-state.json`:
```json
{
  "version": "2.0.0",
  "last_bootstrap": "ISO-date",
  "files_generated": ["list of all generated files"],
  "analysis_fingerprint": "description of codebase state",
  "validation": {
    "total_references": 0,
    "verified_references": 0,
    "confidence_score": 0.0,
    "unverified": ["list of references that could not be verified"]
  }
}
```

If any file has confidence below 70%:
- Re-analyze that section with more focused codebase reading
- Regenerate with corrected references
- Re-validate

Report to user:
- Total files created
- Validation confidence score
- Any unverified references flagged for manual review
- Suggestions for running `/shield`, `/audit`, `/export` next

---

## Quality Principles

1. **Specific over generic** — "Use `zustand` persist middleware for client state" not "Use appropriate state management"
2. **Evidence-based** — Every rule references actual code, config, or patterns found in the project
3. **Actionable** — Every statement tells you what to DO, not just what to think about
4. **Minimal** — No filler, no padding, no "best practices" that everyone already knows
5. **Opinionated** — Take a clear stance. "Do X" not "Consider doing X"
6. **Grounded** — If you can't find evidence for a rule in the codebase, don't invent it
7. **Layered** — Standards build on each other. Don't repeat what's in `core.md` across every file

**The test for every generated file: Would a principal engineer at a frontier company find this useful, or would they delete it as noise?**
