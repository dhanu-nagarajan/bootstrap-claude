# Unified Codebase Analysis Protocol

Used by: /bootstrap, /roast, /explain, /audit, /doctor

This protocol standardizes how every skill analyzes a codebase. Skills that need codebase understanding MUST follow this protocol to ensure consistent, thorough analysis.

---

## Analysis Steps

### 1. Language & Framework Detection

Detect primary language from file extensions + config files:

| Signal | Language | Framework Signal | Framework |
|--------|----------|------------------|-----------|
| tsconfig.json, *.ts | TypeScript | next.config.* | Next.js |
| *.tsx + tsconfig | TypeScript | vite.config.* | Vite/React |
| pyproject.toml, *.py | Python | fastapi in deps | FastAPI |
| requirements.txt, *.py | Python | django in deps | Django |
| Cargo.toml, *.rs | Rust | actix-web in deps | Actix |
| go.mod, *.go | Go | gin in deps | Gin |
| pom.xml, *.java | Java | spring in deps | Spring Boot |
| build.gradle, *.kt | Kotlin | ktor in deps | Ktor |
| *.csproj, *.cs | C# | Microsoft.AspNetCore | ASP.NET |
| Package.swift, *.swift | Swift | Vapor in deps | Vapor |

Detection order:
1. Check for config files (most reliable)
2. Count file extensions (confirms primary language)
3. Parse dependency manifests (identifies framework)
4. Check for monorepo signals (workspaces, lerna, turborepo, nx)

### 2. Structure Mapping

```bash
ls -la                    # root layout
ls src/ 2>/dev/null       # source organization
ls app/ 2>/dev/null       # app directory
ls lib/ 2>/dev/null       # library code
ls packages/ 2>/dev/null  # monorepo packages
ls tests/ test/ spec/ __tests__/ 2>/dev/null  # test organization
```

Map to architecture pattern:
- **Flat** — everything in root (small projects, scripts)
- **Feature-based** — `features/` or co-located by domain
- **Layer-based** — `controllers/`, `services/`, `models/`
- **Domain-driven** — `domains/` or `modules/` with internal layers
- **Monorepo** — `packages/`, `apps/`, or workspace config
- **Framework-convention** — follows framework structure (Next.js `app/`, Rails `app/models/`)

### 3. Convention Extraction

Read 3-5 representative source files (pick from different directories, not just one area). Extract:

- **Naming:** function style (camelCase/snake_case), variable style, file naming (kebab/camel/pascal), class naming
- **Typing:** strict mode enabled, inference patterns, boundary validation approach
- **Imports:** organization (grouped? sorted?), aliasing (@/ paths), barrel files (index.ts re-exports)
- **Error handling:** try/catch patterns, Result/Either types, error boundaries, custom error classes
- **Comments:** density (sparse/moderate/heavy), style (JSDoc/docstrings/inline), documentation patterns

### 4. Dependency Analysis

Parse package manifest. Categorize each significant dependency:

| Category | Examples |
|----------|----------|
| Core framework | Next.js, FastAPI, Actix, Spring Boot |
| State management | zustand, Redux, SQLAlchemy, Diesel |
| Testing | vitest, pytest, cargo test, JUnit |
| Build tooling | vite, webpack, setuptools, cargo |
| Linting/formatting | eslint, ruff, clippy, prettier |
| Authentication | next-auth, passport, jsonwebtoken |
| Database | prisma, drizzle, sqlalchemy, diesel |
| API layer | trpc, graphql, REST clients |
| Observability | sentry, datadog, opentelemetry |

### 5. Git Intelligence

```bash
git log --oneline -20                          # recent activity patterns
git log --format='%s' -50 | head -10           # commit message style
git shortlog -sn --no-merges | head -5         # contributor count
git log --since='3 months ago' --oneline | wc -l  # activity level
```

Extract:
- Commit style (conventional commits? imperative? prefixed?)
- Activity level (active/moderate/dormant)
- Team size (solo/small team/large team)
- Recent focus areas (which directories have most recent changes)

### 6. Existing AI Tool Configuration

Check for existing AI tool configurations:
- `CLAUDE.md` — existing Claude Code rules
- `.cursorrules` or `.cursor/rules/` — Cursor rules
- `.github/copilot-instructions.md` — Copilot instructions
- `AGENTS.md` — Multi-agent instructions
- `.aider.conventions.md` — Aider conventions
- `.clinerules` — Cline rules
- `CONTRIBUTING.md` — contribution guidelines

If found, these inform generation (preserve existing decisions, don't contradict).

### 7. Build & Test Commands

Verify which commands actually exist:

| Source | Check |
|--------|-------|
| package.json scripts | Read scripts object |
| Makefile | Read target names |
| pyproject.toml [tool.poetry.scripts] | Read scripts |
| Cargo.toml | cargo test, cargo build, cargo clippy |
| go.mod | go test, go build, go vet |

Only reference commands that are confirmed to exist.

---

## Output Format

Store findings as structured analysis available to the calling skill. Every finding MUST include the source (file path or command) that produced it. Example:

```
Language: TypeScript (from: tsconfig.json, 847 .ts files)
Framework: Next.js 14 (from: next.config.mjs, "next": "14.2.3" in package.json)
Architecture: Feature-based (from: src/features/ directory structure)
Naming: camelCase functions (from: src/lib/auth.ts, src/api/users.ts, src/hooks/useAuth.ts)
Test framework: Vitest (from: vitest.config.ts, "vitest": "^1.0.0" in package.json)
Test command: pnpm test (from: package.json scripts.test)
```
