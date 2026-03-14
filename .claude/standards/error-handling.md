# Error Handling Standards

## Version Check Failures

**Policy: skip silently, never block.**

`skills/bootstrap/references/version-check.md` line 21: "If fetch fails (network error, rate limit), skip silently — never block the user's workflow."

Applies to all skills that run the version check (bootstrap, update, audit). The version check is informational. If the `WebFetch` to `raw.githubusercontent.com` fails, proceed with the skill as if the check never ran. Do not show error messages, retry prompts, or warnings about the failed check.

## Plugin Self-Update Failures

**Policy: fallback chain with manual reinstall as last resort.**

`skills/update/SKILL.md` Step 4 defines three strategies in order:

1. **Try rsync from local clone** — check common paths (`$HOME/Documents/Github/bootstrap-claude`, etc.). If found and version matches remote, sync.
2. **Try git clone to temp directory** — clone from GitHub, rsync to plugin cache, clean up temp directory.
3. **Fallback: tell user to reinstall manually** — show the uninstall/reinstall commands:
   ```
   /plugin uninstall bootstrap-claude
   /install-plugin https://github.com/dhanu-nagarajan/bootstrap-claude
   ```

Never leave the user without a path forward. If strategies 1 and 2 fail, strategy 3 always works.

## Missing Package Manifest

**Policy: warn and continue with limited features.**

When `core/analysis/codebase-analyzer.md` cannot find a package manifest (`package.json`, `Cargo.toml`, `go.mod`, `pyproject.toml`, etc.):

- Show the warning using the format from `core/output/progress-format.md`:
  ```
  Analyzing codebase...
    ✗ No package manifest found

    ⚠ Cannot detect dependencies without a package manifest.

    Options:
      1. Create a requirements.txt and re-run
      2. Continue anyway (some features will be limited)
      → Run: pip freeze > requirements.txt
  ```
- Continue generation with available information (directory structure, file extensions, git history)
- Generated output will lack dependency-specific conventions but will still include structure, naming, and architecture rules

## Reference Validation Failures

**Policy: retry once below 70%, then flag for manual review.**

`core/validation/reference-validator.md` defines three confidence tiers:

| Score | Action |
|---|---|
| >= 90% | Ship as-is. Mention unverified items for manual review. |
| 70-89% | Review unverified items. Re-analyze sections with low scores. |
| < 70% | Re-analyze affected sections. Regenerate and re-validate. If still low after one retry, flag for manual review. |

Confidence is tracked per generated file. If a single file drops below 70%, re-analyze only that file — not the entire output. After one retry, if still below 70%, include the file with a warning rather than blocking the entire generation.

Adjusted scoring: unverified symbol references count at 0.5x weight (they may exist in generated or external code). File path and command failures count at full weight. See `core/validation/reference-validator.md` lines 61-65.

## Missing Language Example

**Policy: adapt from existing examples, do not block.**

`skills/bootstrap/references/master-prompt.md` Step 3: "If the project uses a language without a reference example, use the examples as calibration for the level of specificity expected, and adapt patterns to the project's ecosystem."

The registry currently has examples for TypeScript, Python, Rust, Go, and Java (`registry/examples/`). For other languages (C#, Swift, Ruby, PHP, etc.):
- Use the existing examples to calibrate the expected depth and specificity
- Apply the same structure (conventions, patterns, anti-patterns) adapted to the target language's ecosystem
- Do not produce shallower output just because no example exists

## Broken References During /update

**Policy: auto-fix moved files, flag deleted files.**

`skills/update/references/update-spec.md` defines handling for reference changes:

- **File renamed/moved** — find all references to old path across `.claude/` files, replace with new path (Reference Replacement strategy)
- **File deleted without replacement** — flag for manual review rather than silently removing the reference
- **Command changed** — replace all occurrences; if command removed without replacement, flag for review
