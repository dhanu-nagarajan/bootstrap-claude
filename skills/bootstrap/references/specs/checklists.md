# Spec: Checklists

Generation specification for `.claude/checklists/` — verification gates that ensure quality at key moments in the development workflow.

---

## Files to Generate

| File | Purpose |
|------|---------|
| `pre-commit.md` | Verification before every commit |
| `new-module.md` | Checklist when adding a major new feature/module |
| `pr-review.md` | Code review criteria (self-review or peer review) |
| `incident.md` | Production incident response protocol |

---

## Critical Generation Rule

**ALL commands MUST be verified against the project's package.json, Makefile, Cargo.toml, or equivalent before inclusion.** If a command does not exist in the project, do NOT include it in any checklist.

To verify: Run Grep against the project's build/task configuration files. Confirm every command you reference produces output (or at minimum, is defined as a script/target).

---

## Pre-Generation Analysis

Before writing any checklist, the generator MUST discover:

1. **Available commands** — Parse `package.json` scripts, `Makefile` targets, `Cargo.toml` metadata, CI config, or equivalent. Build a complete list of commands that actually exist.
2. **Directory structure** — Map the project's module/feature directory layout for the new-module checklist.
3. **Registration patterns** — Identify if the project has registries, barrel exports, routing tables, or other files that must be updated when adding new code.
4. **Linter and formatter config** — Identify what automated checks exist (ESLint, Prettier, Clippy, Black, etc.) so checklists do not duplicate what automation already catches.

---

## `pre-commit.md`

Things to verify BEFORE every commit. Split into automated and manual checks.

### Automated Section

List ONLY commands that exist in the project. Use the exact command syntax:

```markdown
## Automated Checks

- [ ] `{exact typecheck command}` passes
- [ ] `{exact lint command}` passes
- [ ] `{exact test command}` passes
- [ ] `{exact build command}` succeeds
```

**Omit any category that does not have a corresponding command.** If the project has no typecheck command, do not include a typecheck line. If the project has no build step, do not include a build line.

### Manual Section

Project-specific manual checks. Include only checks that are relevant:

```markdown
## Manual Checks

- [ ] No debug code, temporary logging, or TODO hacks in the diff
- [ ] No secrets, API keys, or credentials in the diff
- [ ] No unrelated changes bundled in this commit
- [ ] Commit message explains WHY, not just WHAT
```

Add project-specific items based on analysis:
- If the project has shared components: "All modified shared modules — consumers checked"
- If the project has a public API: "No breaking changes to public API surface"
- If the project has documentation: "Documentation updated if behavior changed"

**Do not add generic manual checks that are not relevant to this project's workflow.**

---

## `new-module.md`

Checklist for adding a major new feature or module. **Entirely project-specific.**

Must reference the project's ACTUAL:

### Directory Structure
- Where to create the new module directory (exact path pattern from the project)
- What files to create within it (derived from existing module structure)
- Naming convention for the directory and files

### Registration Steps
- What files need to be updated to register the new module (routing, barrel exports, config files, plugin registries)
- Reference the specific files by path

### Pattern Conformance
- Which existing module to use as a reference implementation
- What standards files to consult (with specific section references)

### Verification
- Commands to run after adding the module (the project's actual test/lint/build commands)
- How to verify the module is correctly registered and accessible

### Format
```markdown
## New Module Checklist

### Create Structure
- [ ] Create directory at `{actual path pattern}`
- [ ] Create `{file1}` following pattern in `{existing example}`
- [ ] Create `{file2}` following pattern in `{existing example}`

### Register
- [ ] Add to `{specific registry/config file}`
- [ ] Update `{specific barrel export or routing file}`
- [ ] Add to `{specific navigation/menu config}` (if applicable)

### Implement
- [ ] Follow architecture patterns in `.claude/standards/architecture.md`
- [ ] Follow naming conventions in `.claude/standards/language.md`
- [ ] Add error handling per `.claude/standards/error-handling.md`

### Verify
- [ ] `{exact test command}` passes
- [ ] `{exact lint command}` passes
- [ ] New module is accessible/reachable via expected entry point
```

---

## `pr-review.md`

What to check when reviewing code (self-review or peer review). Ordered by priority.

### Review Dimensions

1. **Correctness** — Does it do what it claims? Logic errors, off-by-ones, race conditions, null handling.
2. **Security** — Input validation, auth checks, secret exposure, injection vectors. Reference `.claude/standards/security.md`.
3. **Architecture** — Does it follow established patterns? Does it belong in this location? Reference `.claude/standards/architecture.md`.
4. **Performance** — Unnecessary work, N+1 patterns, unbounded loops, missing pagination. Reference `.claude/standards/performance.md`.
5. **Testing** — Are the right things tested? Are assertions meaningful (not just "it doesn't throw")? Reference `.claude/standards/testing.md`.
6. **Readability** — Naming, structure, comments where needed. Can someone unfamiliar understand this in 5 minutes?
7. **Forbidden patterns** — Check against `.claude/standards/forbidden.md`. Any violations are automatic rejections.

### Project-Specific Review Items

Add items based on what matters for THIS project:
- If API project: "API contract changes are backward-compatible or versioned"
- If library: "Public API surface changes are intentional and documented"
- If frontend: "UI changes work across supported browsers/viewports"
- If data project: "Migrations are reversible"

---

## `incident.md`

Production issue response protocol. Adapt to the project's deployment model.

### Phases

1. **Assess** — Severity and impact scope. Who/what is affected. Is it data loss, degraded service, or cosmetic?
2. **Investigate** — Check monitoring/logs for error patterns. Identify the change that introduced it (deploy history, `git log`, `git bisect`).
3. **Decide** — Rollback vs. hotfix. Rollback if: the bad deploy is identified and rollback is safe. Hotfix if: rollback would cause data issues or the fix is obvious and small.
4. **Communicate** — Status to stakeholders. Expected timeline.
5. **Fix** — Apply fix, verify in staging/preview if available, deploy.
6. **Post-Incident** — Root cause analysis, prevention measures, update monitoring/alerts.

### Project-Specific Adaptations
- Reference the project's actual deployment mechanism (CI/CD pipeline, manual deploy, etc.)
- Reference the project's actual monitoring/logging tools
- Reference the project's actual rollback procedure

If the project is not deployed (e.g., a library or CLI tool), adapt the incident checklist to cover "broken release" scenarios instead of "production outage" scenarios.

---

## Validation Checklist

After generating all checklist files, verify:

- [ ] Every command in `pre-commit.md` exists in the project's task runner config (verified via Grep)
- [ ] Every file path in `new-module.md` exists or follows an existing path pattern (verified via Glob)
- [ ] Every registration step in `new-module.md` references a real file that exists
- [ ] No checklist contains commands that do not exist in the project
- [ ] No checklist contains generic items irrelevant to this project's workflow
- [ ] `incident.md` matches the project's actual deployment model (or is adapted for non-deployed projects)
- [ ] Would a principal engineer follow these checklists, or dismiss them as boilerplate that does not match how the project actually works?
