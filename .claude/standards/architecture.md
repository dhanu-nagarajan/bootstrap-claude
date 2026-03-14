# Architecture Standards

## Layer Map

Three layers, strict dependency direction:

```
core/          Shared infrastructure. Used by ALL skills.
  analysis/      Codebase analysis (language detection, conventions, structure)
  validation/    Reference verification + confidence scoring
  fingerprint/   SHA-256 codebase hashing for drift detection
  output/        Standardized CLI progress and report formatting

skills/        9 skills, each self-contained in skills/{name}/
  bootstrap/     Tiered .claude/ generation (lite/standard/full)
  shield/        Compaction-resilient session protection
  audit/         Standards compliance checking
  doctor/        Drift detection
  update/        Incremental maintenance + plugin self-update
  export/        Cross-tool export (Cursor, Copilot, Aider, Cline, AGENTS.md)
  onboard/       Interactive codebase walkthrough
  roast/         Codebase quality scoring
  explain/       Architecture narrative generation

registry/      Extensible resources. Data only, no logic.
  examples/      Language-specific calibration output (TS, Python, Rust, Go, Java)
  profiles/      Industry/org overlays (fintech, healthcare, startup, open-source)
```

## Dependency Direction

- `skills/` loads from `core/` and `registry/`. Example: `skills/bootstrap/references/master-prompt.md` Step 2 loads `core/analysis/codebase-analyzer.md`; Step 3 loads `registry/examples/*.md`.
- `core/` never imports from `skills/`. Core protocols are consumed by skills, not the reverse.
- `registry/` is pure data. It never imports from `core/` or `skills/`. Registry files are loaded by the master-prompt orchestrator based on detected language and configured profile.

Violations of this direction create circular dependencies that break the plugin's modular loading.

## Skill Module Boundaries

Every skill follows the same structure:

```
skills/{name}/
  SKILL.md              Entry point: YAML frontmatter (name + description) + triggers + flags + execution steps
  references/
    {name}-spec.md      Detailed specification
    *.md                Supporting references (formats, templates, modes)
```

A skill's SKILL.md is the only file the plugin system reads directly. Everything else is loaded via `Read file:` directives within SKILL.md or its referenced specs.

Skills must not read another skill's references directly. Cross-skill invocation goes through the skill's SKILL.md entry point. Example: `/update` runs doctor internally by loading `skills/doctor/references/doctor-spec.md` (see `skills/update/SKILL.md` Step 1), but this is the defined integration point, not an ad-hoc reference.

## Extension Model

Three extension points, each with a template file:

| Extension Type | Template | Location for Custom | Resolution Order |
|---|---|---|---|
| Language examples | `registry/examples/EXAMPLE-TEMPLATE.md` | `.bootstrap/examples/` | project-local > built-in (per `registry/registry-spec.md`) |
| Industry profiles | `registry/profiles/PROFILE-TEMPLATE.md` | `.bootstrap/profiles/` | project-local > built-in |
| Export formats | `skills/export/references/formats/FORMAT-TEMPLATE.md` | `.bootstrap/formats/` | project-local > built-in |

Custom resources override built-in ones by name. They never modify built-in files.

## Key Architectural Files

| File | Role |
|---|---|
| `.claude-plugin/plugin.json` | Plugin identity: name, version (3.0.0), description. Bump version for releases. |
| `skills/bootstrap/references/master-prompt.md` | Generation orchestrator. Controls the 10-step flow: tier selection, analysis, example loading, profile loading, spec loading, file generation, CLAUDE.md update, auto-export, validation, reporting. |
| `core/analysis/codebase-analyzer.md` | Shared analysis protocol. Language detection table (32 framework signatures), structure mapping, convention extraction, git intelligence, command verification. Used by bootstrap, roast, explain, audit, doctor. |
| `core/validation/reference-validator.md` | Reference verification. Three validation types (file paths, commands, code symbols), confidence scoring with per-file thresholds, adjusted weighting for symbols at 0.5x. |
| `registry/registry-spec.md` | Extension system definition. Resolution order, resource types (specs, profiles, examples, formats), custom resource structure, configuration via `.bootstrap-config.yaml`. |
