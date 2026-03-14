# Update Specification: Incremental Maintenance Methodology

This document defines how to detect user-edited sections, merge generated content with customizations, compute fingerprints, and handle rollback scenarios.

## User-Edited Section Detection

### Marker Format

User-edited sections are delimited by HTML comments:

```markdown
<!-- user-edited -->
This content was written or modified by the user.
It must be preserved exactly during updates.
<!-- /user-edited -->
```

### Detection Rules

1. **Explicit markers** — Content between `<!-- user-edited -->` and `<!-- /user-edited -->` is always preserved
2. **No markers present** — If a file has no markers, treat the entire file as generated content eligible for updates
3. **Partial markers** — If only opening marker exists without closing, treat everything from the marker to the next heading (## or #) as user-edited
4. **Nested markers** — Not supported; markers must be at the same nesting level

### Handling During Updates

When updating a file with user-edited sections:

1. Parse the file into segments: `[generated, user-edited, generated, user-edited, ...]`
2. Regenerate only the `generated` segments
3. Reassemble: interleave regenerated content with preserved user-edited segments
4. Verify the reassembled file is valid markdown (headings hierarchy intact, no broken links)

## Merge Strategies

### Additive Merge (Default)

Used for: `architecture.md`, `language.md`, `testing.md`, `performance.md`, context files.

**Algorithm:**
1. Parse existing file into sections by heading
2. For each section in the existing file, check if it needs updating
3. For new content that needs adding, find the appropriate insertion point (after the most relevant existing section)
4. Insert new sections with a comment: `<!-- added by /update [date] -->`
5. Do not remove or rewrite existing sections unless they contain broken references

**Example:**
```markdown
## Module: Authentication    <!-- existing, preserved -->
...

## Module: Webhooks          <!-- added by /update 2025-01-15 -->
New module handling incoming webhook events from...
```

### Reference Replacement

Used for: broken file paths, renamed commands, updated config values.

**Algorithm:**
1. Identify the exact broken reference and its replacement
2. Use Edit tool to replace only the broken reference, preserving all surrounding text
3. If multiple replacements in the same file, batch them into a single edit operation

**Example:**
```
Old: `src/services/auth.ts`
New: `src/modules/auth/service.ts`
```

### Section Regeneration

Used for: `error-handling.md` sections, checklists with outdated commands.

**Algorithm:**
1. Identify the section boundaries (heading to next heading of same or higher level)
2. Re-analyze the relevant codebase aspect
3. Generate new section content
4. Replace the entire section (heading to next heading)
5. Preserve any user-edited subsections within the regenerated section

### Preserve-Only

Used for: `forbidden.md`, `security.md`.

**Algorithm:**
1. Read the existing file
2. Identify new rules that should be added (from codebase analysis)
3. Append new rules at the end of the relevant section
4. Never remove existing rules — security and forbidden rules are append-only
5. If a rule appears to be outdated, flag it for manual review rather than removing

## Analysis Fingerprint Computation

The fingerprint captures structural codebase state for change detection.

### What to Include

1. **Source file tree** — Sorted list of all source file paths
   ```
   Glob("src/**/*") + Glob("lib/**/*") + Glob("app/**/*")
   ```
   Exclude: `node_modules`, `.git`, `dist`, `build`, `coverage`, `.next`, `__pycache__`, `.cache`

2. **Package manifest** — Full content of:
   - `package.json` (dependencies + devDependencies + scripts sections)
   - Or `Cargo.toml`, `go.mod`, `requirements.txt`, `pyproject.toml`

3. **Config files** — Full content of:
   - TypeScript config (`tsconfig.json`)
   - Linter config (`.eslintrc*`, `.prettierrc*`)
   - Build config (`webpack.config.*`, `vite.config.*`, `next.config.*`)
   - CI config (`.github/workflows/*.yml`, `.gitlab-ci.yml`)
   - Docker config (`Dockerfile`, `docker-compose.yml`)

4. **Directory structure** — Sorted list of directories at depth 1 and 2

### Computation

```
input = sort(file_tree).join("\n")
      + "\n---MANIFEST---\n" + manifest_content
      + "\n---CONFIG---\n" + sort(config_contents).join("\n")
      + "\n---DIRS---\n" + sort(directories).join("\n")

fingerprint = SHA-256(input)  # hex encoded
```

Use a Bash command to compute: `echo -n "$input" | sha256sum | cut -d' ' -f1`

### Comparison

When comparing fingerprints:
- **Match** — Codebase structure unchanged since last update. Report "no structural drift detected."
- **Mismatch** — Something changed. Proceed with full analysis to determine what.

## Update Strategies by Change Type

### New Module Detected

1. Analyze the new module: read its files, understand its purpose
2. Add a section to `architecture.md` describing the module's role, boundaries, and dependencies
3. If the module has tests, verify they match `testing.md` conventions
4. If the module introduces new patterns, add them to `language.md`

### New Dependency Detected

1. Identify the dependency's role (framework, utility, testing, etc.)
2. Add relevant conventions to the appropriate standards file
3. If it replaces an existing dependency, flag the old one for review

### Command Changed

1. Find all references to the old command across `.claude/` files
2. Replace with the new command
3. If the command was removed without replacement, flag for manual review

### File Renamed/Moved

1. Find all references to the old path across `.claude/` files
2. Replace with the new path
3. If the file was deleted without replacement, flag for manual review

## Rollback Guidance

If an update causes problems:

### Immediate Rollback

1. Use `git diff .claude/` to see all changes made by the update
2. Use `git checkout -- .claude/` to revert all changes
3. The `.bootstrap-state.json` will also revert, restoring the previous fingerprint

### Selective Rollback

1. Use `git diff .claude/specific-file.md` to see changes to one file
2. Use `git checkout -- .claude/specific-file.md` to revert just that file
3. Manually update `.bootstrap-state.json` to remove the file from `files_updated`

### Prevention

1. Always commit before running /update so rollback is trivial
2. Review diffs before confirming changes in the "requires re-analysis" tier
3. Run /audit after /update to verify the updates did not introduce inconsistencies

## Edge Cases

### First Update (No State File)

If `.claude/.bootstrap-state.json` does not exist:
1. Treat all `.claude/` files as baseline
2. Run full analysis as if everything is new
3. Create the state file with current state
4. Set `last_bootstrap` to the git commit date of the earliest `.claude/` file

### Conflicting Changes

If a user-edited section conflicts with a necessary update (e.g., user wrote a custom architecture section but the module was deleted):
1. Preserve the user-edited section
2. Add a warning comment above it: `<!-- warning: module X referenced below may no longer exist -->`
3. Flag in the summary for manual review

### Empty .claude/ Files

If a `.claude/` file exists but is empty:
1. Treat it as needing full regeneration
2. Re-analyze the relevant aspect and generate content
3. Note in the summary that the file was regenerated from scratch
