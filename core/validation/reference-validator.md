# Reference Validation Protocol

Used by: /bootstrap (Step 7), /doctor, /update, /audit

Every reference in generated `.claude/` files MUST be verified against the actual codebase. This protocol defines how.

---

## Validation Types

### 1. File Path Validation

Extract all backticked paths from generated content (patterns like `src/lib/auth.ts`, `app/api/route.ts`).

For each path:
1. **Exact match** — Glob for the exact path → **VERIFIED**
2. **Fuzzy match** — Glob for `**/basename` → **MOVED** (suggest path update)
3. **No match** → **UNVERIFIED** (flag for review or removal)

Exclude from validation:
- Paths that are clearly example/template placeholders (contain `{{`, `<placeholder>`)
- Paths in code blocks marked as examples
- External URLs

### 2. Command Validation

Extract all backticked commands (patterns matching common CLI invocations).

Common patterns to detect:
- `npm run <script>`, `pnpm <script>`, `yarn <script>`, `bun <script>`
- `cargo <subcommand>`, `cargo test`, `cargo clippy`
- `python -m <module>`, `pytest`, `mypy`
- `go test`, `go build`, `go vet`, `golangci-lint`
- `make <target>`
- `dotnet <subcommand>`
- `./gradlew <task>`, `mvn <goal>`

Verification sources:
| Command Type | Where to Check |
|---|---|
| npm/pnpm/yarn/bun scripts | package.json `scripts` object |
| Makefile targets | Makefile target definitions |
| Python tools | pyproject.toml `[tool.poetry.scripts]` or `[project.scripts]`, or check if package is in dependencies |
| Cargo commands | Built-in (test, build, clippy) always valid; aliases in `.cargo/config.toml` |
| Go commands | Built-in (test, build, vet) always valid |
| dotnet commands | Built-in always valid; custom in *.csproj |

### 3. Code Symbol Validation

Extract referenced code symbols (function names, type names, class names):
- **PascalCase words** → likely types, classes, components, interfaces
- **camelCase words** → likely functions, methods, variables
- **snake_case words** → likely Python/Rust/Go functions
- **SCREAMING_SNAKE** → likely constants, enum values

For each symbol:
1. Grep source files for the symbol name
2. If found in a declaration context (function def, class def, type def, const) → **VERIFIED**
3. If found only in usage context → **LIKELY** (lower confidence)
4. If not found → **UNVERIFIED**

Skip common language keywords and well-known library names.

---

## Confidence Scoring

```
confidence = (verified_count / total_references) * 100
```

### Thresholds

| Score | Level | Action |
|-------|-------|--------|
| >= 90% | HIGH | Ship as-is. Mention unverified items for manual review. |
| 70-89% | MEDIUM | Review unverified items. Re-analyze sections with low scores. |
| < 70% | LOW | Re-analyze affected sections with more focused codebase reading. Regenerate. Re-validate. |

### Per-File Scoring

Track confidence per generated file, not just overall:
```
standards/architecture.md: 95% (19/20 refs verified)
standards/language.md: 88% (22/25 refs verified)
standards/testing.md: 100% (8/8 refs verified)
checklists/pre-commit.md: 75% (3/4 commands verified)
```

If any individual file drops below 70%, that specific file should be re-analyzed and regenerated.

---

## Reporting

### For Users

Show a summary after validation:
```
Validation: 94% confidence (127/135 references verified)
  HIGH: standards/architecture.md (95%), standards/testing.md (100%)
  MEDIUM: checklists/pre-commit.md (75%)
  Unverified:
    - src/legacy/old-helper.ts (file not found)
    - npm run integration-test (not in package.json scripts)
    - formatCurrency (function not found in source)
```

### For State File

Record in `.bootstrap-state.json`:
```json
{
  "validation": {
    "total_references": 135,
    "verified_references": 127,
    "confidence_score": 0.94,
    "unverified": [
      {"ref": "src/legacy/old-helper.ts", "type": "file_path", "in_file": "standards/architecture.md"},
      {"ref": "npm run integration-test", "type": "command", "in_file": "checklists/pre-commit.md"},
      {"ref": "formatCurrency", "type": "symbol", "in_file": "standards/language.md"}
    ]
  }
}
```
