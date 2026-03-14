# New Language Example Template

Adds a language calibration example to the registry. 5 existing examples: TypeScript, Python, Rust, Go, Java.

## File to Create

```
registry/examples/{{language}}-project.md
```

## Starting Point

Use `registry/examples/EXAMPLE-TEMPLATE.md` as the base structure. Fill in:

- **Naming conventions** — case style (camelCase, snake_case, PascalCase), file naming, module naming
- **Framework detection signals** — config files, import patterns, directory conventions (e.g., `Cargo.toml` for Rust, `pyproject.toml` for Python)
- **Typical project structure** — standard directory layout for the language/ecosystem
- **Example generated output** — sample `.claude/` content at the quality bar, showing language-specific standards and patterns
- **Common anti-patterns** — language-specific forbidden patterns to include in generated `forbidden.md`

## Quality Bar

Compare against existing examples for calibration:
- `registry/examples/typescript-project.md` — framework-heavy, config-heavy ecosystem
- `registry/examples/python-project.md` — multiple packaging systems, virtual environments
- `registry/examples/rust-project.md` — strict type system, ownership patterns
- `registry/examples/go-project.md` — simplicity-oriented, strong conventions
- `registry/examples/java-project.md` — enterprise patterns, build tool variety

The new example should be at the same depth and specificity as these.

## Post-Scaffolding Checklist

1. **`skills/bootstrap/references/master-prompt.md`** — add language to Step 3 language detection/matching list
2. **README.md** — add to "Supported Languages" section
3. **CLAUDE.md** — add to directory tree under `registry/examples/`
4. **Test** — run `/bootstrap` on a project using this language, verify generated output uses language-specific conventions
