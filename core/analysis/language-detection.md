# Language & Framework Detection Protocol

Detailed detection rules for identifying project language, framework, and ecosystem.

---

## Detection Priority

1. **Config files** (highest confidence) — tsconfig.json means TypeScript
2. **Package manifest** (high confidence) — dependencies reveal framework
3. **File extension counts** (medium confidence) — confirms primary language
4. **Directory conventions** (supporting) — app/ in Next.js, src/main/java in Maven

## Language-Specific Detection

### TypeScript / JavaScript
- **Config:** tsconfig.json (TS), jsconfig.json (JS), package.json
- **Framework signals in package.json dependencies:**
  - `next` → Next.js (check for app/ vs pages/ router)
  - `react` without `next` → React SPA (check for vite/webpack)
  - `express` → Express.js
  - `@nestjs/core` → NestJS
  - `nuxt` → Nuxt.js
  - `svelte` or `@sveltejs/kit` → SvelteKit
  - `astro` → Astro
- **Typing:** Check tsconfig.json strict mode, check for `@types/` packages
- **Package manager:** yarn.lock (Yarn), pnpm-lock.yaml (pnpm), package-lock.json (npm), bun.lockb (Bun)

### Python
- **Config:** pyproject.toml, setup.py, setup.cfg, requirements.txt, Pipfile
- **Framework signals in dependencies:**
  - `fastapi` → FastAPI
  - `django` → Django
  - `flask` → Flask
  - `sqlalchemy` → SQLAlchemy ORM
  - `pydantic` → Pydantic validation
  - `celery` → Celery task queue
- **Typing:** Check for `mypy` or `pyright` in dev deps, `py.typed` marker
- **Package manager:** poetry (pyproject.toml [tool.poetry]), pip, pipenv, uv

### Rust
- **Config:** Cargo.toml, rust-toolchain.toml
- **Framework signals in Cargo.toml dependencies:**
  - `actix-web` → Actix Web
  - `axum` → Axum
  - `tokio` → Async runtime
  - `diesel` → Diesel ORM
  - `clap` → CLI application
  - `serde` → Serialization
- **Type checking:** Rust is always typed; check clippy config for strictness

### Go
- **Config:** go.mod, go.sum
- **Framework signals in go.mod require:**
  - `github.com/gin-gonic/gin` → Gin
  - `github.com/gorilla/mux` → Gorilla
  - `github.com/labstack/echo` → Echo
  - `google.golang.org/grpc` → gRPC
  - `github.com/spf13/cobra` → CLI (Cobra)
- **Conventions:** Go enforces most conventions; focus on project-specific patterns

### Java / Kotlin
- **Config:** pom.xml (Maven), build.gradle / build.gradle.kts (Gradle)
- **Framework signals:**
  - `org.springframework` → Spring Boot
  - `io.quarkus` → Quarkus
  - `io.micronaut` → Micronaut
  - `io.ktor` → Ktor (Kotlin)
- **Build:** Maven (`mvn`), Gradle (`./gradlew`)

### C# / .NET
- **Config:** *.csproj, *.sln, Directory.Build.props
- **Framework signals in csproj PackageReference:**
  - `Microsoft.AspNetCore` → ASP.NET Core
  - `Microsoft.EntityFrameworkCore` → EF Core
  - `xunit` or `NUnit` → Testing
- **Build:** `dotnet build`, `dotnet test`

### Swift
- **Config:** Package.swift, *.xcodeproj
- **Framework signals:**
  - `Vapor` → Vapor web framework
  - `SwiftUI` → iOS/macOS UI
  - `Combine` → Reactive framework

## Multi-Language Projects

When multiple languages are detected:
1. Identify the **primary** language (most source files, main application)
2. Identify **secondary** languages (scripts, tooling, specific services)
3. Generate standards for primary language with notes about secondary
4. In monorepos, each package may have its own primary language

## Output

Report:
- Primary language + version constraints (if detectable)
- Framework + version
- Package manager
- Build system
- Type checking configuration
- Detected conventions specific to this ecosystem
