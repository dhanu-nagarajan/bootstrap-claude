# Doctor Specification: Drift Detection Methodology

This document defines how to extract references from `.claude/` files, fingerprint codebase state, detect coverage gaps, and classify drift severity.

## Reference Extraction from Markdown

### File Path References

Extract paths from backtick-delimited text that match file path patterns.

**Regex patterns to use:**
- Backtick paths: `` `[a-zA-Z0-9_./-]+\.[a-zA-Z]+` `` (contains dot + extension)
- Backtick directory paths: `` `[a-zA-Z0-9_/-]+/` `` (ends with slash)
- Code block commands with file args: lines in fenced code blocks containing path-like arguments

**Filtering:**
- Exclude URLs (starting with `http://` or `https://`)
- Exclude common non-path backtick content: single words without `/` or `.ext` suffix
- Exclude markdown formatting artifacts
- Exclude example/placeholder paths that use obvious placeholders like `your-file`, `example.ts`

**Validation:**
For each extracted path:
1. Check if it exists exactly as written: `Glob` with the exact path
2. If not found, check if the filename exists elsewhere: `Glob` with `**/{filename}`
3. If found elsewhere, classify as "moved" rather than "missing"

### Command References

Extract commands from backticks and code blocks.

**Identification patterns:**
- Backtick commands starting with known CLI tools: `npm`, `yarn`, `pnpm`, `npx`, `make`, `cargo`, `go`, `python`, `pip`, `docker`, `kubectl`, `git`
- Lines in fenced code blocks that start with `$` or common CLI prefixes
- `package.json` script references: `npm run [script-name]`, `yarn [script-name]`

**Validation:**
1. For `npm run X` / `yarn X` / `pnpm X`: check `package.json` scripts for `X`
2. For `make X`: check `Makefile` for target `X`
3. For `cargo X`: check if it is a built-in cargo command or defined in `Cargo.toml`
4. For bare commands: check if they are standard system commands (skip these)

### Code Symbol References

Extract function names, type names, and class names from backticks.

**Identification patterns:**
- PascalCase words in backticks (likely types/classes): `` `UserService` ``, `` `AuthMiddleware` ``
- camelCase words in backticks (likely functions): `` `handleRequest` ``, `` `validateInput` ``
- Qualified names: `` `module.functionName` ``

**Validation:**
Use `Grep` to search for the symbol in source files. Check for:
- Function declarations: `function symbolName`, `def symbol_name`, `fn symbol_name`
- Class/type declarations: `class SymbolName`, `type SymbolName`, `interface SymbolName`
- Variable assignments: `const symbolName`, `let symbolName`, `var symbolName`

**Tolerance:** Only flag a symbol as missing if it appears nowhere in the codebase. Symbols that appear in different forms (e.g., renamed but still referenced) should be flagged as "possibly renamed."

## Codebase Fingerprinting

The fingerprint captures the structural state of the codebase for drift comparison.

**Components to fingerprint:**
1. **File tree** — Sorted list of all source file paths (excluding node_modules, .git, build artifacts)
2. **Package manifest** — Contents of package.json, Cargo.toml, go.mod, requirements.txt, or equivalent
3. **Config files** — Contents of tsconfig.json, .eslintrc, Dockerfile, CI config files
4. **Directory structure** — Top-level and second-level directory names

**Fingerprint computation:**
1. Concatenate all components in deterministic order
2. Compute SHA-256 hash of the concatenation
3. Store as hex string in `.bootstrap-state.json`

**What triggers fingerprint mismatch:**
- New or deleted source files
- Changed dependencies
- Changed configuration
- New or removed directories

**What does NOT trigger mismatch:**
- Source code changes within existing files (content changes do not affect file tree)
- Git metadata changes

## Coverage Gap Detection

### Directory Coverage

1. List directories at depth 1 and 2 under the source root
2. Read `.claude/standards/architecture.md`
3. For each directory, check if its name appears anywhere in architecture.md
4. Unreferenced directories are coverage gaps

**Exceptions:** Skip common infrastructure directories that do not need architecture documentation: `__tests__`, `__mocks__`, `node_modules`, `.git`, `dist`, `build`, `coverage`, `.next`, `.cache`

### Dependency Coverage

1. Read the package manifest to extract all dependencies
2. Read all `.claude/standards/` files
3. For each dependency, check if its name appears in any standards file
4. Focus on direct dependencies only (not transitive)

**Prioritization:**
- Framework dependencies (react, express, django) — HIGH priority to document
- Utility libraries (lodash, date-fns) — MEDIUM priority
- Dev-only dependencies (testing, linting) — LOW priority

### File Pattern Coverage

1. Use `Glob` with `**/*` to collect all file extensions in the project
2. Read `.claude/standards/testing.md` and `.claude/standards/language.md`
3. Check if each extension/pattern is addressed
4. Focus on application-specific patterns, not universal ones (.json, .md, .yml)

## Severity Classification for Drift

| Severity | Type | Example |
|---|---|---|
| HIGH | Broken file reference | Standards reference a deleted file |
| HIGH | Missing command | Checklist references a removed npm script |
| HIGH | Broken code symbol | Standards reference a renamed/deleted function |
| MEDIUM | New untracked module | New `src/webhooks/` directory with no architecture docs |
| MEDIUM | New major dependency | Added `graphql` but no standards mention it |
| LOW | Stale timestamp | Last bootstrap was 30+ days ago |
| LOW | Minor dep uncovered | New utility library not mentioned |
| INFO | Config change | Updated tsconfig target but standards still reference old one |

## Overall Health Assessment

Calculate health status from findings:

- **HEALTHY** — 0 HIGH severity issues, fewer than 3 MEDIUM issues
- **NEEDS UPDATE** — 1-3 HIGH severity issues, or 3-5 MEDIUM issues
- **STALE** — More than 3 HIGH severity issues, or more than 5 MEDIUM issues, or last update was 60+ days ago

## Differential Reporting

When a previous `.claude/doctor-report.md` exists:

1. Read the previous report
2. Compare findings — identify which issues are new, which are resolved, which persist
3. Add a "Changes Since Last Check" section:
   - Fixed: issues present last time but resolved now
   - New: issues not present last time
   - Persistent: issues present both times (flag for attention)
