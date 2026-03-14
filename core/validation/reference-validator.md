# Reference Validation Protocol

Used by: /bootstrap (Step 7), /doctor, /update, /audit

Every reference in generated `.claude/` files MUST be verified against the actual codebase.

## Validation Types

### 1. File Path Validation

Extract all backticked paths (e.g. `src/lib/auth.ts`).

For each path:
1. **Exact match** — Glob for the exact path → **VERIFIED**
2. **Fuzzy match** — Glob for `**/basename` → **MOVED** (suggest path update)
3. **No match** → **UNVERIFIED** (flag for review or removal)

Exclude from validation:
- Paths containing `{{` or `<placeholder>` (template placeholders)
- Paths in code blocks marked as examples
- External URLs

### 2. Command Validation

Extract all backticked commands matching common CLI invocations (`npm run`, `cargo`, `pytest`, `make`, `go test`, `dotnet`, `./gradlew`, `mvn`, etc.).

Verification sources:
| Command Type | Where to Check |
|---|---|
| npm/pnpm/yarn/bun scripts | package.json `scripts` object |
| Makefile targets | Makefile target definitions |
| Python tools | pyproject.toml `[project.scripts]` or dependencies |
| Cargo commands | Built-in (test, build, clippy) always valid; aliases in `.cargo/config.toml` |
| Go commands | Built-in (test, build, vet) always valid |
| dotnet commands | Built-in always valid; custom in *.csproj |

### 3. Code Symbol Validation

Extract referenced code symbols:
- **PascalCase** → types, classes, components, interfaces
- **camelCase** → functions, methods, variables
- **snake_case** → Python/Rust/Go functions
- **SCREAMING_SNAKE** → constants, enum values

For each symbol:
1. Grep source files for the symbol name
2. Found in a declaration context (function/class/type/const def) → **VERIFIED**
3. Found only in usage context → **LIKELY** (lower confidence)
4. Not found → **UNVERIFIED**

Skip common language keywords and well-known library names.

## Confidence Scoring

```
confidence = verified_count / total_references * 100
```

Files with 0 extractable references (pure methodology content) default to 100%.

**Adjusted weighting:** Unverified symbol references count at 0.5x weight (they may exist in generated or external code). File path and command failures count at full weight.

```
adjusted = (verified + unverified_symbols * 0.5) / total * 100
```

### Thresholds

| Score | Level | Action |
|-------|-------|--------|
| >= 90% | HIGH | Ship as-is. Mention unverified items for manual review. |
| 70-89% | MEDIUM | Review unverified items. Re-analyze sections with low scores. |
| < 70% | LOW | Re-analyze affected sections. Regenerate and re-validate. If still low after one retry, flag for manual review. |

Track confidence **per generated file**. If any individual file drops below 70%, re-analyze that file specifically.

## Reporting

After validation, show: overall score with fraction, per-file scores grouped by level, and each unverified reference with its type and containing file.
