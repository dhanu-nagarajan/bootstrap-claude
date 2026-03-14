# Codebase Analysis Spec

Used by: /bootstrap, /roast, /explain, /audit, /doctor

## 1. Language & Framework Detection

Detection order: config files → dependency manifests → file extension counts → directory conventions.

| Config | Language | Dependency Signal | Framework |
|--------|----------|-------------------|-----------|
| tsconfig.json | TypeScript | `next` | Next.js (check app/ vs pages/) |
| tsconfig.json | TypeScript | `react` (no next) | React SPA |
| tsconfig.json | TypeScript | `@nestjs/core` | NestJS |
| package.json (no TS) | JavaScript | `express` | Express |
| package.json | JS/TS | `nuxt` | Nuxt |
| package.json | JS/TS | `svelte`/`@sveltejs/kit` | SvelteKit |
| package.json | JS/TS | `astro` | Astro |
| pyproject.toml / requirements.txt | Python | `fastapi` | FastAPI |
| pyproject.toml / requirements.txt | Python | `django` | Django |
| pyproject.toml / requirements.txt | Python | `flask` | Flask |
| Cargo.toml | Rust | `actix-web` | Actix |
| Cargo.toml | Rust | `axum` | Axum |
| Cargo.toml | Rust | `clap` | CLI app |
| go.mod | Go | `gin-gonic/gin` | Gin |
| go.mod | Go | `labstack/echo` | Echo |
| go.mod | Go | `spf13/cobra` | CLI (Cobra) |
| go.mod | Go | `google.golang.org/grpc` | gRPC |
| pom.xml | Java | `org.springframework` | Spring Boot |
| build.gradle(.kts) | Kotlin/Java | `io.ktor` | Ktor |
| *.csproj | C# | `Microsoft.AspNetCore` | ASP.NET Core |
| Package.swift | Swift | `Vapor` | Vapor |

Package manager detection: yarn.lock → Yarn, pnpm-lock.yaml → pnpm, package-lock.json → npm, bun.lockb → Bun, poetry.lock → Poetry, Pipfile.lock → Pipenv, uv.lock → uv.

Monorepo signals: workspaces in package.json, lerna.json, turbo.json, nx.json. Each package may have its own primary language.

Multi-language projects: identify primary (most source files) and secondary languages. Generate for primary with notes on secondary.

## 2. Structure Mapping

```bash
ls -la
ls src/ app/ lib/ packages/ 2>/dev/null
ls tests/ test/ spec/ __tests__/ 2>/dev/null
```

Architecture patterns: **Flat** (root-level), **Feature-based** (features/ or co-located by domain), **Layer-based** (controllers/services/models), **Domain-driven** (domains/ or modules/ with internal layers), **Monorepo** (packages/, apps/), **Framework-convention** (Next.js app/, Rails app/models/).

## 3. Convention Extraction

Read 3-5 non-trivial source files from different directories, preferring recently committed business-logic files. Extract:

- **Naming:** functions (camelCase/snake_case), files (kebab/camel/pascal), constants (SCREAMING_SNAKE?), interfaces/types (I-prefix? T-suffix?)
- **Imports:** grouped (external→internal→relative)? Sorted? Path aliases (@/)? Barrel files? Separate `import type`?
- **Error handling:** try/catch vs Result types, custom error classes, structured logging, recovery strategy
- **Structure:** typical file/function length, nesting depth, export style (named/default), comment density

Cross-reference: if 4/5 files agree, that's the convention. If split, note inconsistency. Linter config overrides observed patterns.

### Linter Config Locations

| Language | Config Files |
|----------|-------------|
| JS/TS | `.eslintrc*`, `eslint.config.*`, `.prettierrc*` |
| Python | `ruff.toml`, `pyproject.toml [tool.ruff]`, `pyproject.toml [tool.mypy]` |
| Rust | `clippy.toml`, `rustfmt.toml` |
| Go | `.golangci.yml` |

### Type Checking

- TS: tsconfig.json `strict`, presence of `@types/` packages
- Python: `mypy`/`pyright` in dev deps, `py.typed` marker
- Rust: always typed; check clippy strictness level

## 4. Git Intelligence

```bash
git log --oneline -20
git log --format='%s' -50 | head -10
git shortlog -sn --no-merges | head -5
git log --since='3 months ago' --oneline | wc -l
```

Extract: commit style (conventional? imperative? prefixed?), activity level, team size, recent focus areas.

## 5. Existing AI Tool Config

Check and preserve decisions from:
- `CLAUDE.md`, `.cursorrules`, `.cursor/rules/`, `.github/copilot-instructions.md`
- `AGENTS.md`, `.aider.conventions.md`, `.clinerules`, `CONTRIBUTING.md`

## 6. Build & Test Command Verification

| Source | Check |
|--------|-------|
| package.json | `scripts` object |
| Makefile | target names |
| pyproject.toml | `[tool.poetry.scripts]` or `[project.scripts]` |
| Cargo.toml | `cargo test`, `cargo build`, `cargo clippy` |
| go.mod | `go test`, `go build`, `go vet` |
| *.csproj | `dotnet build`, `dotnet test` |

Only reference commands confirmed to exist. Every finding must cite its source (file path or command).
