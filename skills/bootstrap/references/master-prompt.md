# Bootstrap: Master Orchestrator v3

You are a Principal Software Engineer bootstrapping an autonomous engineering system for this project. Generate a `.claude/` directory structure tailored to THIS codebase, at the tier level specified.

**Quality bar: Every generated file must reflect the standards of a senior engineer at Anthropic, Apple, or Google. No filler. No generic advice. Every sentence specific, actionable, grounded in what you discover about THIS project.**

---

## Step 1: Determine Tier

Check what tier was requested:
- `--lite` → Load `${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/tiers/lite.md`
- `--standard` (default) → Load `${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/tiers/standard.md`
- `--full` → Load `${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/tiers/full.md`

The tier spec defines exactly which files to generate. Follow it.

If `--only <categories>` was specified, generate only those categories regardless of tier.
If `--exclude <categories>` was specified, skip those categories.

---

## Step 2: Deep Project Analysis

Before generating anything, perform comprehensive analysis. Use the shared analysis protocol:

```
Read file: ${CLAUDE_PLUGIN_ROOT}/core/analysis/codebase-analyzer.md
```

Launch subagents in parallel for speed. Discover:

1. **Package manifest** — `package.json`, `Cargo.toml`, `go.mod`, `requirements.txt`, `pyproject.toml`, `Gemfile`, `pom.xml`, or equivalent
2. **Config files** — Language config, linters, formatters, build tools, CI/CD, Docker
3. **Directory structure** — Map the architecture via `ls` on root and key subdirectories
4. **Entry points** — Main application entry, routing, API surface
5. **Existing conventions** — Read 3-5 representative source files using the convention extraction protocol:
   ```
   Read file: ${CLAUDE_PLUGIN_ROOT}/core/analysis/convention-extraction.md
   ```
6. **Existing rules** — Check for `CLAUDE.md`, `.cursorrules`, `.cursor/rules/`, `.github/copilot-instructions.md`, `AGENTS.md`, `CONTRIBUTING.md`
7. **Git history** — `git log --oneline -20` for development patterns and commit style
8. **Test infrastructure** — Framework, file conventions, how to run
9. **Dependencies** — Key libraries and their architectural roles
10. **Bootstrap config** — Check for `.bootstrap-config.yaml` for tier, profile, and overrides

If a `CLAUDE.md` already exists, preserve its project-specific content. Do not overwrite user-written sections.

Store all findings. You will reference them in every file you generate.

---

## Step 3: Detect Language & Load Examples

Identify the project's primary language using:

```
Read file: ${CLAUDE_PLUGIN_ROOT}/core/analysis/language-detection.md
```

Then read the corresponding reference example for calibration:

- **TypeScript/JavaScript**: `${CLAUDE_PLUGIN_ROOT}/registry/examples/typescript-project.md`
- **Python**: `${CLAUDE_PLUGIN_ROOT}/registry/examples/python-project.md`
- **Rust**: `${CLAUDE_PLUGIN_ROOT}/registry/examples/rust-project.md`
- **Go**: `${CLAUDE_PLUGIN_ROOT}/registry/examples/go-project.md`
- **Java/Kotlin**: `${CLAUDE_PLUGIN_ROOT}/registry/examples/java-project.md`

If the project uses a language without a reference example, use the examples as calibration for the level of specificity expected, and adapt patterns to the project's ecosystem.

---

## Step 4: Load Profile (if configured)

If `.bootstrap-config.yaml` exists and specifies a `profile`, load it:

**Built-in profiles** (check `${CLAUDE_PLUGIN_ROOT}/registry/profiles/`):
- `fintech` → `${CLAUDE_PLUGIN_ROOT}/registry/profiles/fintech.md`
- `healthcare` → `${CLAUDE_PLUGIN_ROOT}/registry/profiles/healthcare.md`
- `startup` → `${CLAUDE_PLUGIN_ROOT}/registry/profiles/startup.md`
- `open-source` → `${CLAUDE_PLUGIN_ROOT}/registry/profiles/open-source.md`

**Custom profiles** (file path in config):
- If profile value starts with `./` or `/`, read it as a file path

Apply the profile's additions during generation. Profile rules layer ON TOP of the base generation — they add, they don't replace.

---

## Step 5: Load Custom Specs (if configured)

If `.bootstrap-config.yaml` specifies `specs.include`, load each additional spec:
- Built-in name → check `${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/specs/`
- File path → read directly

These produce additional files in `.claude/` beyond what the tier defines.

---

## Step 6: Generate Files

Follow the tier spec loaded in Step 1. For each category, load its generation spec:

### Standards
```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/specs/standards.md
```
Generate the standards files specified by the tier.

### Shield
```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/shield/references/shield-spec.md
```
Generate shield files (all tiers include shield).

### Checklists (standard + full tiers)
```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/specs/checklists.md
```
Generate the checklist files specified by the tier.

### Context (standard + full tiers)
```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/specs/context.md
```
Generate the context files specified by the tier.

### Protocols (full tier only)
```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/specs/protocols.md
```

### Templates (full tier only)
```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/specs/templates.md
```

### Personas (full tier only)
```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/specs/personas.md
```

**Adapt to the project:**
- Skip `standards/components.md` if no UI layer exists
- Skip UI-related persona details for backend-only projects
- Templates must match what this project actually builds repeatedly
- All commands in checklists must be the project's verified actual commands
- ADRs in `context/decisions.md` inferred from actual codebase patterns

---

## Step 7: Update CLAUDE.md

Read the recovery template:
```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/shield/references/recovery-template.md
```

Update the project's `CLAUDE.md` with:
1. The post-compaction recovery protocol (at the TOP for maximum survival during compaction)
2. A routing table covering all generated directories

If `CLAUDE.md` already has project-specific content (overview, commands, architecture), preserve it. Add the operational system above the existing content.

---

## Step 8: Auto-Export (if configured)

If `.bootstrap-config.yaml` has `export.auto_export: true` and `export.targets` specified, automatically run the export transformation for each target after generation.

---

## Step 9: Validate

**This is NOT optional. Every reference must be verified.**

Use the shared validation protocol:

```
Read file: ${CLAUDE_PLUGIN_ROOT}/core/validation/reference-validator.md
```

For each generated file:
1. Extract all file paths in backticks → verify each exists with Glob
2. Extract all commands → verify each exists in package.json scripts / Makefile targets / equivalent
3. Extract all function/type/class names → verify each exists with Grep
4. Track: `references_verified` / `references_total` per file

Use the confidence scoring methodology:

```
Read file: ${CLAUDE_PLUGIN_ROOT}/core/validation/confidence-scorer.md
```

If any file has confidence below 70%:
- Re-analyze that section with more focused codebase reading
- Regenerate with corrected references
- Re-validate

Generate `.claude/.bootstrap-state.json`:
```json
{
  "version": "3.0.0",
  "tier": "{lite|standard|full}",
  "last_bootstrap": "ISO-date",
  "files_generated": ["list of all generated files"],
  "analysis_fingerprint": "sha256:...",
  "profile": "{profile name or null}",
  "custom_specs": ["list of custom specs loaded"],
  "validation": {
    "total_references": 0,
    "verified_references": 0,
    "confidence_score": 0.0,
    "per_file": {
      "standards/forbidden.md": {"total": 0, "verified": 0, "confidence": 0.0},
      ...
    },
    "unverified": [
      {"ref": "...", "type": "file_path|command|symbol", "in_file": "..."}
    ]
  }
}
```

---

## Step 10: Report

Use the standardized progress output format:

```
Read file: ${CLAUDE_PLUGIN_ROOT}/core/output/progress-format.md
```

Report to user:
- Tier used (lite/standard/full)
- Total files created
- Validation confidence score (overall and per-file)
- Any unverified references flagged for manual review
- Profile applied (if any)
- Custom specs loaded (if any)
- Suggestions for next steps:
  - `/audit` to check compliance
  - `/export` to export to other tools
  - `/shield` to refresh session protection
  - Upgrade suggestion if not on full tier

---

## Quality Principles

1. **Specific over generic** — "Use `zustand` persist middleware for client state" not "Use appropriate state management"
2. **Evidence-based** — Every rule references actual code, config, or patterns found in the project
3. **Actionable** — Every statement tells you what to DO, not just what to think about
4. **Minimal** — No filler, no padding, no "best practices" that everyone already knows
5. **Opinionated** — Take a clear stance. "Do X" not "Consider doing X"
6. **Grounded** — If you can't find evidence for a rule in the codebase, don't invent it
7. **Layered** — Standards build on each other. Don't repeat what's in `core.md` across every file
8. **Progressive** — Lite tier proves value fast. Users upgrade when they want more.

**The test for every generated file: Would a principal engineer at a frontier company find this useful, or would they delete it as noise?**
