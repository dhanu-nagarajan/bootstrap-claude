# Export Transformation Specification

General principles for transforming `.claude/` engineering standards into other AI tool formats.

## Transformation Principles

### Condense, Don't Truncate

The `.claude/` system is verbose by design — Claude Code has a large context window. Other tools do not. When transforming:

- Merge related rules into single statements where meaning is preserved
- Remove explanatory paragraphs — keep only the directive
- Convert prose to bullet lists
- Drop examples unless they clarify an otherwise ambiguous rule
- Eliminate redundancy across files (`.claude/` files intentionally repeat key points)

**Example:**
- Source: "Always use named exports rather than default exports. Default exports make refactoring harder because the import name is decoupled from the export name, leading to inconsistent naming across the codebase."
- Export: "Use named exports, never default exports."

### Priority Ordering

When space is limited, include rules in this order:

1. **Forbidden patterns** — What to NEVER do (prevents damage)
2. **Security rules** — Authentication, authorization, input validation
3. **Architecture boundaries** — Module structure, dependency direction, layer separation
4. **Naming and typing conventions** — How to name things, type signatures
5. **Error handling patterns** — How to handle and propagate errors
6. **Testing conventions** — How to write and organize tests
7. **Performance guidelines** — Optimization rules
8. **Workflow/process** — Pre-commit steps, PR conventions (omit for non-agentic tools)

### Tool-Specific Adaptation

Each tool has different capabilities. Adapt accordingly:

| Capability         | Cursor | Copilot | Aider | Cline | AGENTS.md |
| ------------------ | ------ | ------- | ----- | ----- | --------- |
| Agentic workflow   | Partial| No      | Yes   | Yes   | Partial   |
| File-scoped rules  | Yes    | No      | No    | No    | Yes       |
| Multiple rule files| Yes    | No      | No    | No    | Yes       |
| Context budget     | Medium | Small   | Small | Medium| Large     |
| Glob matching      | Yes    | No      | No    | No    | Yes       |

### Preserve Intent, Not Structure

The `.claude/` system uses specific structures (protocols, checklists, personas) that have no equivalent in other tools. Transform by intent:

- Protocols -> fold key directives into general rules
- Checklists -> convert to "before committing, verify..." rules
- Personas -> omit entirely (other tools don't support persona switching)
- Context files -> extract only what helps the tool write better code

### Handle Missing Sources

Not every project will have all `.claude/` files. Transform what exists:

- If `forbidden.md` is missing, check `core.md` for any "never" / "do not" rules
- If `architecture.md` is missing, infer basic structure from the directory tree
- If only 1-2 standards files exist, still generate the export — partial coverage is better than none
- Note gaps in the export report so the user knows what is missing

## Token Budget Guidelines

Approximate target sizes for each format:

| Format   | Target Lines | Rationale                                    |
| -------- | ------------ | -------------------------------------------- |
| Cursor   | 50-200/file  | Multiple files, each loaded by glob match    |
| Copilot  | 150-300      | Single file, always in context               |
| Aider    | 100-200      | Included in every prompt                     |
| Cline    | 200-400      | Single file, agentic so needs more detail    |
| AGENTS.md| 200-500      | Per-directory, can be larger                 |
