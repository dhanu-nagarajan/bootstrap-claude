# Spec: Context

Generation specification for `.claude/context/` — domain knowledge and architectural decisions that explain WHAT we are building and WHY decisions were made.

---

## Files to Generate

| File | Purpose |
|------|---------|
| `domain.md` | Business domain, terminology, rules |
| `decisions.md` | Architecture Decision Records (ADR log) |
| `glossary.md` | Project-specific terms and definitions |

---

## Critical Generation Rule

**Every claim in context files must be traceable to actual code.** Context files are not essays — they are reference documents. Every business rule, every term, every decision must point back to a file, function, type, or commit that serves as evidence.

If you cannot find code evidence for a claim, do not include it. A shorter, evidence-backed context file is always better than a longer speculative one.

---

## Pre-Generation Analysis

Before writing any context file, the generator MUST:

1. **Read the README** — Extract project purpose, user-facing description, and stated goals.
2. **Read entry points** — Understand what the project does from the code itself (main files, route definitions, CLI entry points, exported APIs).
3. **Scan for domain language** — Grep for recurring terms in type names, function names, variable names, comments, and documentation strings.
4. **Read git history** — `git log --oneline -30` to understand development trajectory and recent decisions.
5. **Read existing context files** — Check for existing `CLAUDE.md`, `CONTRIBUTING.md`, `ARCHITECTURE.md`, design docs, or ADRs.

---

## `domain.md`

Document the business domain this project operates in. Every section must reference actual code.

### Required Sections

#### Problem Statement
- What problem does this project solve? Derive from README and code, not assumptions.
- Who are the users? Identify from UI strings, API design, or documentation.

#### Core Domain Concepts
For each concept:
```markdown
### [Concept Name]

**Definition:** [Precise definition]
**Represented by:** `src/types/concept.ts` — `ConceptType` interface
**Created in:** `src/api/concepts.ts:createConcept()`
**Business rules:**
- [Rule 1] — enforced in `src/validators/concept.ts:42`
- [Rule 2] — enforced in `src/api/concepts.ts:87`
```

**Every business rule MUST include a `file:line` reference** to where it is enforced in code. If you cannot find where a rule is enforced, either the rule does not exist or it is unenforced (document it as a gap, not as a fact).

#### Domain Calculations / Algorithms
- Document any non-trivial business logic, calculations, or algorithms.
- Reference the specific function that implements each one.
- Explain the business reason for the calculation, not just the math.

#### External Systems
- What external services, APIs, databases, or systems does the project interact with?
- Reference the integration code for each (client libraries, API calls, database connections).
- Note any rate limits, authentication requirements, or data format constraints.

---

## `decisions.md`

An Architecture Decision Record log. Generate 3-5 ADRs inferred from the codebase.

### How to Infer Decisions

Decisions are embedded in the codebase as:
1. **Dependency choices** — Why this framework and not another? Check `package.json`/equivalent for the stack.
2. **Directory structure** — Why organized this way? The structure IS the architectural decision.
3. **Pattern choices** — Why this pattern for error handling, state management, or data flow? Read the code.
4. **Configuration choices** — Why these settings? Check tsconfig, linter config, build config for intentional strictness choices.
5. **Git history** — Major refactors visible in commit history often represent decisions.

### ADR Format

```markdown
## ADR-NNN: [Decision Title]

- **Date:** [From git history if traceable, otherwise "Pre-existing"]
- **Status:** Accepted
- **Context:** [The problem or forces that led to this decision]
- **Decision:** [What was chosen and why]
- **Consequences:** [What this enables and what it constrains]
- **Evidence:** [Specific files/configs/commits that demonstrate this decision]
```

### Requirements

- **Every ADR MUST include an Evidence field** with specific file or commit references.
- Generate 3-5 ADRs. Do not pad with trivial decisions ("ADR: We use Git" is not useful).
- Focus on decisions that a new developer would need to understand to work effectively.
- If a decision has visible trade-offs (something is harder because of it), document those honestly.

### Good ADR Candidates
- Framework/language choice (and what it rules out)
- Monorepo vs. multi-repo structure
- Directory organization philosophy (feature-based vs. layer-based)
- Testing strategy (what is tested, what is not)
- Build/deploy toolchain choices
- State management approach (if frontend)
- Database/storage choices (if applicable)
- API design philosophy (REST vs. GraphQL vs. RPC, versioning)

---

## `glossary.md`

Project-specific terms that a new developer would need to understand.

### Derivation Process

1. **Scan type definitions** — Type names, interface names, enum values often encode domain language.
2. **Scan function names** — Public function names reveal the project's vocabulary.
3. **Scan comments and docstrings** — Domain terms are often explained inline.
4. **Scan README and docs** — User-facing terminology.
5. **Scan UI strings** — Labels, titles, error messages reveal how the domain is presented to users.

### Format

Every entry MUST include a code reference:

```markdown
**Term** -- Definition. See: `file:line`
```

If a term appears in multiple locations, reference the most authoritative one (typically a type definition or constant declaration).

### Categories

Group terms by category if the project has distinct domains:

```markdown
## Core Concepts
**Widget** -- A configurable UI element. See: `src/types/widget.ts:5`

## API Terms
**Endpoint** -- A REST route handler. See: `src/routes/index.ts:12`

## Internal Jargon
**Hydration** -- The process of populating a Widget with data. See: `src/core/hydrate.ts:1`
```

### Requirements

- Minimum 5 terms, maximum 25. Aim for terms a new developer would actually look up.
- Do not include universally known programming terms ("function", "variable", "class").
- Do not include framework-standard terms that are documented in the framework's own docs.
- Include abbreviations and acronyms used in the codebase with their expansions.

---

## Validation Checklist

After generating all context files, verify:

- [ ] Every `file:line` reference in `domain.md` exists and contains what is claimed (verify via Grep)
- [ ] Every ADR in `decisions.md` has an Evidence field with verifiable references
- [ ] Every glossary entry has a `See: file:line` reference that exists
- [ ] No business rule is stated without a code reference showing where it is enforced
- [ ] No ADR describes a decision without file/config/commit evidence
- [ ] No glossary term is universally known (only project-specific terms)
- [ ] All referenced files exist (verify via Glob)
- [ ] Content is derived from actual codebase analysis, not assumptions about what the project "probably" does
- [ ] Would a principal engineer starting on this project find these context files genuinely useful for ramp-up, or dismiss them as noise?
