# Codebase Fingerprinting Protocol

Used by: /doctor, /update to detect codebase changes since last generation.

---

## Fingerprint Computation

### Input Data

Collect these elements in order:

1. **File tree** — Sorted list of all source files (excluding .git, node_modules, __pycache__, build artifacts, .claude/)
2. **Package manifest** — Full content of package.json / Cargo.toml / go.mod / pyproject.toml / equivalent
3. **Config files** — Content of: tsconfig.json, eslint config, prettier config, Docker config, CI config (sorted by filename)
4. **Directory structure** — Sorted list of directories at depth 1 and 2

### Computation

```
fingerprint = SHA-256(file_tree + "\n---\n" + manifest + "\n---\n" + configs + "\n---\n" + directories)
```

### Storage

Store in `.bootstrap-state.json`:
```json
{
  "analysis_fingerprint": "sha256:a1b2c3d4...",
  "fingerprint_inputs": {
    "file_count": 847,
    "manifest": "package.json",
    "config_files": ["tsconfig.json", ".eslintrc.js", "next.config.mjs"],
    "directory_count": 23
  }
}
```

## Comparison

### Same Fingerprint
No structural changes detected. Skip re-analysis unless forced with `--force`.

### Different Fingerprint
Structural changes detected. Proceed with drift analysis:
1. Compare file trees to find added/removed/moved files
2. Compare manifests to find dependency changes
3. Compare configs to find setting changes
4. Compare directories to find structural changes

### No Previous Fingerprint
First run or state file missing. Generate fingerprint and store. Treat as full analysis needed.

## Exclusion Patterns

Always exclude from fingerprint computation:
- `.git/`
- `node_modules/`, `vendor/`, `venv/`, `.venv/`, `target/`, `build/`, `dist/`
- `__pycache__/`, `.pytest_cache/`, `.mypy_cache/`
- `.claude/` (our own generated files)
- `*.log`, `*.tmp`, `*.swp`
- Lock files (these change too frequently and don't indicate structural changes)
