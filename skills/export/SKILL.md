---
name: export
description: >-
  This skill should be used when the user asks to "export to cursor",
  "generate cursorrules", "copilot instructions", "export standards",
  "export to aider", "export for cline", "cross-tool export", "sync to cursor",
  "generate AGENTS.md", or invokes /export. It exports the project's .claude/
  standards and conventions to other AI coding tool formats.
---

# Export: Cross-Tool Standards Sync

Transform the project's `.claude/` engineering system into formats consumed by other AI coding tools. Preserves intent while adapting structure, verbosity, and features to each target tool's capabilities.

**Quality bar:** Exported rules must be immediately useful in the target tool — not a raw copy-paste of `.claude/` content, but a thoughtful adaptation that respects each tool's context budget and feature set.

## Execution

### Step 1: Prerequisites Check

Verify the `.claude/standards/` directory exists and contains at least one standards file.

If `.claude/standards/` does not exist:
- Tell the user: "No `.claude/` engineering system found. Run `/bootstrap` first to analyze your codebase and generate standards."
- Stop execution.

If `.claude/standards/` exists but is sparse (fewer than 3 files), warn the user that exports will be limited, then proceed.

### Step 2: Detect Target

Parse the user's request for the target tool. Supported targets:

| Target    | Output                          | Description              |
| --------- | ------------------------------- | ------------------------ |
| `cursor`  | `.cursor/rules/*.mdc`           | Multiple rule files      |
| `copilot` | `.github/copilot-instructions.md` | Single instructions file |
| `aider`   | `.aider.conventions.md`         | Conventions file         |
| `cline`   | `.clinerules`                   | Single rules file        |
| `agents`  | `AGENTS.md`                     | Repository agent guide   |
| `all`     | All of the above                | Full export              |

If the user did not specify a target, ask: "Which tool should I export to? Options: cursor, copilot, aider, cline, agents, or all."

### Step 3: Load Source Material

Read all available source files. Not all will exist in every project — use what is available:

- `.claude/standards/*.md` — All standards files (architecture, language, testing, security, forbidden, etc.)
- `.claude/protocols/core.md` — Core operating principles and workflow
- `.claude/standards/forbidden.md` — Hard rejections and anti-patterns
- `.claude/checklists/pre-commit.md` — Pre-commit verification rules
- `.claude/context/domain.md` — Domain context (useful for architecture descriptions)
- `CLAUDE.md` — Project overview and commands

Build a unified understanding of the project's rules before transforming. Prioritize:
1. Forbidden patterns (highest priority — these prevent damage)
2. Architecture rules (structural correctness)
3. Language/coding conventions (consistency)
4. Testing standards (quality gates)
5. Workflow/process rules (lowest priority — not all tools support agentic work)

### Step 4: Transform

Read the format specification for the target tool:

```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/export/references/formats/{target}.md
```

Also read the general transformation principles:

```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/export/references/export-spec.md
```

Transform the source material following the format spec. Key principles:
- Condense — target tools generally have smaller context budgets than Claude Code
- Adapt structure — each tool has its own format expectations
- Preserve intent — the rules must mean the same thing in the new format
- Prioritize — if space is limited, keep forbidden patterns and architecture rules, drop workflow

### Step 5: Write Output

Generate the target file(s). Create parent directories if they do not exist (e.g., `.cursor/rules/`, `.github/`).

For `all` target: generate each format sequentially, reusing the loaded source material.

### Step 6: Verify

For each generated file:
1. Confirm the file exists on disk
2. Check it is non-empty
3. Verify it follows the target format (frontmatter for `.mdc`, markdown structure for others)
4. Confirm no `.claude/`-specific references leaked into the output (the target tool does not know about `.claude/`)

### Step 7: Report

Summarize what was exported:
- List each generated file with its path and approximate line count
- Note any standards that were omitted due to space constraints
- Suggest the user review the output and adjust tool-specific settings if needed

## Quality Principles

1. **Adapted, not copied** — Each export is rewritten for its target tool's strengths
2. **Actionable in context** — Rules reference the project's actual code, not generic advice
3. **Space-conscious** — Respect each tool's context budget; ruthlessly condense
4. **Complete coverage** — Forbidden patterns and security rules are never omitted
5. **No leakage** — Exported files never reference `.claude/` internals or Claude-specific concepts
