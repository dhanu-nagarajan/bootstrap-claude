# Spec: Standards

Generation specification for `.claude/standards/` — the quality bar that defines WHAT good code looks like in a given project.

---

## Files to Generate

| File | Purpose | Skip Condition |
|------|---------|----------------|
| `architecture.md` | Layer boundaries, dependency rules, module contracts | Never skip |
| `language.md` | Language-specific conventions, naming, typing, idioms | Never skip |
| `components.md` | UI component patterns | Skip if no UI layer |
| `testing.md` | Testing philosophy, patterns, coverage expectations | Never skip |
| `error-handling.md` | Error classification, recovery, logging | Never skip |
| `performance.md` | Performance budgets, optimization rules | Never skip |
| `security.md` | Security checklist, input validation, auth, secrets | Never skip |
| `forbidden.md` | Hard anti-patterns that trigger immediate rejection | Never skip |

---

## Critical Generation Rule

**Every standard must reference patterns ACTUALLY FOUND in this project.** Do not write generic advice. Read the code, find the patterns, codify them.

**If you cannot find evidence for a rule in the codebase, DO NOT generate it.** A shorter, accurate standards file is always better than a longer one full of aspirational rules the project does not follow.

---

## VALIDATION REQUIREMENT (applies to ALL standards files)

After generating each standards file, perform this verification pass:

1. **For every file path referenced** — Run `ls` or Glob to confirm it exists.
2. **For every function/type/variable referenced** — Run Grep to confirm it exists in the codebase.
3. **For every pattern described** — Identify at least one concrete file that demonstrates it.
4. **For every command referenced** — Confirm it exists in the project's scripts/config.

If a reference cannot be verified, either fix it or remove the claim. Do not leave unverified references.

---

## `architecture.md`

Must include, derived from actual codebase analysis:

- **Layer map** — What each directory/module owns. What it may and may not import. Use actual directory names.
- **Dependency direction** — Which layers depend on which. State the "clean architecture" rule as it actually exists (not as ideal theory).
- **Module boundaries** — How to decide what goes where. When to create a new module vs. extend existing. Reference existing precedent.
- **Data flow** — How data moves through the system (request lifecycle, state flow, event propagation). Trace at least one concrete flow.
- **Extension points** — How to add new features following existing patterns. Reference a recent addition as the model to follow.

---

## `language.md`

Named for the project's primary language (e.g., `typescript.md`, `python.md`, `go.md`).

Must include:

- **Naming conventions** — Variables, functions, types, files, directories. **Derive from actual codebase patterns by reading 3-5 representative files.** Show real examples from the code.
- **Type discipline** — Strictness level, generics usage, nullability handling. Reference the project's tsconfig/eslint/mypy/equivalent config.
- **Idioms** — Preferred patterns for common operations (iteration, error handling, async, data transformation). **Show actual code snippets FROM the codebase as examples** — not invented ones.
- **Import organization** — How imports are ordered and grouped. Derive from existing files.

**Code example requirement:** Every idiom described must include a real snippet from the codebase with a file reference. Format: "See `src/foo/bar.ts:42` for an example."

---

## `components.md` (skip if no UI layer)

Must include:

- **Component classification** — Server vs. client, presentational vs. container, page vs. feature vs. shared. Use the project's actual classification.
- **Composition patterns** — How components compose. Props drilling vs. context vs. store. Reference actual examples.
- **State management rules** — When to use local state vs. global store vs. URL state vs. server state. Derived from existing patterns.
- **Styling conventions** — How styles are applied. Reference the actual styling system in use.
- **Accessibility baseline** — Minimum a11y requirements. Reference any existing a11y patterns found.

---

## `testing.md`

Must include:

- **Testing framework and commands** — Exact commands to run tests. What framework is used. Derived from config files.
- **Test file conventions** — Where tests live, naming patterns, file structure. Derived from existing test files.
- **What to test** — Behavior, not implementation. Public API, not internals. Happy path + edge cases + error cases.
- **What NOT to test** — Framework internals, third-party library behavior, trivial getters/setters.
- **Mocking rules** — When mocking is acceptable, preferred mocking patterns. Reference existing mock usage.
- **Coverage expectations** — Philosophy, not a number. "All business logic must have tests" over "80% coverage."

---

## `error-handling.md`

Must include:

- **Error classification** — Operational errors (expected, recoverable) vs. programmer errors (bugs). How each is handled in THIS project.
- **Error boundaries** — Where errors are caught. What layer is responsible for user-facing messages.
- **Logging** — What to log, at what level, with what context. Reference the project's actual logging library/patterns.
- **Recovery strategies** — Retry logic, fallback behavior, graceful degradation. Reference existing patterns.

---

## `performance.md`

Must include:

- **Budget** — What matters for this project type (bundle size, TTFB, render time, API latency, memory, startup time).
- **Lazy loading rules** — What to code-split, what to preload, dynamic import patterns. If not applicable, say so.
- **Memoization rules** — When to memoize (expensive computation, referential equality) vs. when it is premature.
- **Known expensive operations** — Project-specific things that are slow and how they are handled.
- **Measurement** — How to measure performance in this project. What tools, what benchmarks exist.

---

## `security.md`

Must include:

- **Input validation** — Where and how inputs are validated. Sanitization patterns used.
- **Authentication/Authorization** — How auth works (if applicable). Skip if not relevant.
- **Secrets management** — Where secrets live, how they are accessed. What NEVER to commit.
- **Common vulnerabilities** — Prevention specific to this project's stack (XSS, injection, CSRF, etc.).
- **Dependency security** — How to audit deps. Known vulnerability checking tools in use.

---

## `forbidden.md`

A hard-reject list. Things that are NEVER acceptable in this codebase.

### Derivation Requirements

1. **From linter config** — Read ESLint/Prettier/Ruff/Clippy/equivalent config. Every `error`-level rule is a candidate for the forbidden list. Cite the config file.
2. **From dependency analysis** — Identify deprecated or banned packages. Check for `.npmrc` restrictions or equivalent.
3. **From codebase patterns** — Find patterns that are conspicuously absent (e.g., no `any` types, no `console.log`, no inline styles). Their absence is evidence of a rule.
4. **From existing documentation** — Check `CONTRIBUTING.md`, existing `CLAUDE.md`, or code review comments for stated prohibitions.

### Format

```markdown
- `[pattern]` -- [rationale]. Source: [linter rule / convention / observed absence]
```

**Do not invent forbidden patterns that have no evidence in the codebase.** A short, accurate list is better than a long speculative one.

---

## Validation Checklist

After generating all standards files, verify:

- [ ] Every file path referenced exists (verify via Glob or ls)
- [ ] Every function/type/variable name referenced exists in the codebase (verify via Grep)
- [ ] Every code snippet shown is from an actual file, not fabricated
- [ ] Every command referenced exists in the project's scripts/config
- [ ] `language.md` contains real code snippets with file references, not invented examples
- [ ] `forbidden.md` rules are derived from linter config or observed patterns, not invented
- [ ] No file contains generic advice that could apply to any project — every rule is project-specific
- [ ] Standards that reference non-existent project features have been skipped (e.g., `components.md` when there is no UI)
- [ ] Would a principal engineer find each standard actionable for THIS specific project, or delete it as generic noise?
