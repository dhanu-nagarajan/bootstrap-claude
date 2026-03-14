# Language Standards

This project's "language" is Markdown specification authoring. Every file is Markdown, JSON, or YAML. There is no programming language, no build system, no runtime.

## File Naming

| Pattern | Usage | Examples |
|---|---|---|
| `SKILL.md` (uppercase) | Skill entry points only | `skills/bootstrap/SKILL.md`, `skills/shield/SKILL.md` |
| `*-spec.md` | Specification documents | `shield-spec.md`, `audit-spec.md`, `roast-spec.md` |
| `*-template.md` | Template files for extension | `EXAMPLE-TEMPLATE.md`, `PROFILE-TEMPLATE.md`, `FORMAT-TEMPLATE.md`, `recovery-template.md` |
| `*-format.md` | Output format definitions | `progress-format.md`, `report-format.md` |
| lowercase-kebab | Directories | `codebase-analyzer/`, `reference-validator/` |
| lowercase | Simple spec files and format files | `cursor.md`, `copilot.md`, `lite.md`, `standard.md` |

## SKILL.md Frontmatter

Every SKILL.md starts with YAML frontmatter containing exactly two fields:

```yaml
---
name: shield
description: >-
  This skill should be used when the user asks to "protect session",
  "shield", "guard against compaction"...
---
```

The `description` field contains trigger phrases — the plugin system uses these to match user intent. Write trigger phrases as quoted user utterances. See `skills/shield/SKILL.md` lines 1-11 for the canonical example.

## Heading Hierarchy

- **H1 (`#`)** — Document title. One per file. Format: `# {Skill}: {Subtitle}` for SKILL.md files (e.g., `# Shield: Context Compaction Protection System`), `# {Topic}` for specs.
- **H2 (`##`)** — Major sections. Steps in execution flows use `## Step N: {Name}`.
- **H3 (`###`)** — Subsections within a step or section.
- **H4 (`####`)** — Rare. Only when H3 subsections need further breakdown.

Never skip levels (H1 to H3 without H2).

## Cross-References

Reference other plugin files using the `Read file:` directive inside a fenced code block:

```
Read file: ${CLAUDE_PLUGIN_ROOT}/path/to/file.md
```

`${CLAUDE_PLUGIN_ROOT}` resolves to the plugin install directory at runtime. Never hardcode absolute paths. See `skills/bootstrap/references/master-prompt.md` Steps 1-6 for examples of this pattern used throughout.

For references to user-project files (generated `.claude/` output), use project-relative paths without the plugin root variable: `.claude/shield/invariants.md`, `.claude/standards/forbidden.md`.

## Quality Bar

Every specification must pass this test, stated in `skills/bootstrap/SKILL.md` line 121:

> Would a principal engineer find this useful, or delete it as noise?

Operationally this means:
- Every claim must cite a file path from this repository
- Every rule must be actionable ("Do X" not "Consider X")
- No generic advice that applies to any project
- No filler sentences restating what was just said

## Tables for Structured Data

Use Markdown tables for:
- Flags and their effects (`skills/bootstrap/SKILL.md` lines 33-43)
- Scoring thresholds (`core/validation/reference-validator.md` lines 69-73)
- Detection matrices (`core/analysis/codebase-analyzer.md` lines 11-31)
- Update strategies by file type (`skills/update/SKILL.md` Step 3 table)
- Dimensions and rubrics (`skills/roast/references/roast-spec.md`)

Always include a header row. Align pipes for readability.

## Token Budgets

Where specified, these are hard constraints:
- `invariants.md` — under 2000 tokens (~150 lines). Source: `skills/shield/references/shield-spec.md` line 11.
- `recovery-protocol.md` — under 50 lines. Source: `skills/shield/references/shield-spec.md` line 182.

Exceeding these budgets defeats the purpose of the shield system (quick re-read after compaction).

## Imperative Mood

Specifications use imperative mood: "Generate the file", "Extract all paths", "Verify each reference". Match the voice in `skills/shield/references/shield-spec.md` and `core/validation/reference-validator.md`.

Avoid passive voice ("The file should be generated") and hedging ("You might want to consider").
