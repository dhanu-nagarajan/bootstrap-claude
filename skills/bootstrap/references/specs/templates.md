# Spec: Templates

Generation specification for `.claude/templates/` — scaffolding skeletons for code patterns this project creates repeatedly.

---

## Files to Generate

Generate **2-4 template files** based on what this project actually builds repeatedly. No more, no fewer. Each template must earn its existence by matching a real, recurring pattern.

---

## Pre-Generation Analysis

Before writing any template, the generator MUST perform these analyses:

### 1. Git History Pattern Detection

Run `git log --diff-filter=A --name-only --pretty=format:'' | sort | head -100` (or equivalent) to identify files that are frequently created together. Look for clusters:

- Files created in the same commit often represent a "unit of work" — this is a template candidate.
- Example: if `src/routes/X.ts`, `src/routes/X.test.ts`, and `src/types/X.ts` are always created together, that is a "new route" template.

### 2. Structural Repetition Detection

Scan the codebase for directories with similar internal structure:

- Multiple directories with the same file layout (e.g., every module has `index.ts`, `types.ts`, `utils.ts`) indicate a template.
- Use Glob patterns to find these: `**/index.ts`, `**/types.ts`, etc.

### 3. Naming Convention Analysis

Identify the project's actual naming conventions for new files:

- File casing: `kebab-case.ts`, `PascalCase.tsx`, `snake_case.py`
- Directory naming: singular vs. plural, grouped by feature vs. type
- Export patterns: default export vs. named exports, barrel files

---

## Template Content Requirements

Each template file MUST include:

### Header
```markdown
# Template: [Descriptive Name]

Use when: [specific trigger — e.g., "adding a new API endpoint"]
```

### Files to Create

List every file that should be created, with actual directory paths from THIS project:

```markdown
## Files to Create

| File | Purpose |
|------|---------|
| `src/routes/{{name}}.ts` | Route handler |
| `src/routes/{{name}}.test.ts` | Route tests |
| `src/types/{{name}}.ts` | Type definitions |
```

### Skeleton Code

Provide the actual skeleton for each file, using `{{PLACEHOLDER}}` markers that match the project's naming conventions:

- `{{name}}` — kebab-case name (if project uses kebab-case)
- `{{Name}}` — PascalCase name (if project uses PascalCase)
- `{{NAME}}` — UPPER_CASE name (if project uses constants)

**Skeleton code must match the project's actual patterns** — import style, export style, error handling patterns, typing conventions. Derive from existing files, do not invent.

### Post-Scaffolding Checklist

Steps to complete after creating files from the template:

```markdown
## After Scaffolding

- [ ] Register in [specific registry file, if one exists]
- [ ] Update [specific config/routing file]
- [ ] Add to [specific barrel export, if used]
- [ ] Write tests covering [specific test expectations]
- [ ] Verify with `[actual project test command]`
```

### Standards References

```markdown
## Relevant Standards

- Architecture: `.claude/standards/architecture.md` — [specific section]
- Naming: `.claude/standards/language.md` — [specific section]
```

---

## Template Selection Criteria

A pattern qualifies as a template ONLY if:

1. **Frequency** — The project has created 3+ instances of this pattern (evidence from codebase).
2. **Consistency** — Existing instances follow a recognizable structure (not ad-hoc each time).
3. **Complexity** — Creating an instance involves 2+ files or non-obvious registration steps.
4. **Active** — The pattern is still in use (not deprecated or abandoned).

If the project has fewer than 2 qualifying patterns, generate only what qualifies. Do not pad with speculative templates.

---

## Common Template Types (use as inspiration, not prescription)

Only generate templates that match patterns found in THIS project:

- **New API endpoint/route** — Handler, validation, types, tests
- **New component** — Component file, styles, tests, stories
- **New module/feature** — Directory structure, index, types, tests
- **New data model/schema** — Model definition, migration, validation, seed data
- **New CLI command** — Command handler, argument parsing, help text, tests
- **New test file** — Test structure matching the project's conventions

---

## Anti-Patterns

- **Do not generate templates for patterns that appear only once** in the codebase.
- **Do not generate templates with placeholder commands** — every command must be the project's real command.
- **Do not generate templates that contradict existing patterns** — if the project uses default exports, the template must use default exports.
- **Do not generate more than 4 templates** — if unsure, generate fewer. Quality over quantity.

---

## Validation Checklist

After generating template files, verify:

- [ ] Each template matches a pattern that appears 3+ times in the codebase
- [ ] All directory paths in "Files to Create" sections use the project's actual directory structure
- [ ] All `{{PLACEHOLDER}}` markers match the project's actual naming conventions
- [ ] Skeleton code import styles match the project's existing import patterns
- [ ] Post-scaffolding checklist commands are the project's real commands (verified against package manifest)
- [ ] Registration steps reference actual registry/config files that exist
- [ ] No more than 4 templates generated
- [ ] Would a principal engineer use these templates, or find them out of sync with how the project actually works?
