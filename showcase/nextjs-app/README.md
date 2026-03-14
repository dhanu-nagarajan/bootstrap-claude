# Showcase: Next.js Application

This showcase demonstrates what bootstrap-claude generates for a typical Next.js 14 application.

**Hypothetical project:** A SaaS dashboard with user authentication, team management, and a billing system.

**Stack:** Next.js 14 (App Router), TypeScript, Prisma, NextAuth, Tailwind CSS, Vitest

**Tier:** standard (14 files)

## Generated Files

```
.claude/
├── standards/
│   ├── forbidden.md       ← 12 TypeScript/Next.js anti-patterns
│   ├── architecture.md    ← App Router layer boundaries
│   ├── language.md        ← Naming conventions from codebase
│   ├── testing.md         ← Vitest + Testing Library patterns
│   └── security.md        ← NextAuth + API route security
├── checklists/
│   ├── pre-commit.md      ← pnpm test, tsc, eslint commands
│   └── pr-review.md       ← Review dimensions
├── context/
│   ├── decisions.md       ← ADRs (App Router choice, Prisma over Drizzle, etc.)
│   └── glossary.md        ← Project terms (Workspace, Member, Plan, etc.)
└── shield/
    ├── invariants.md      ← Top 12 rules, compaction-proof
    ├── session-state.md   ← Session tracking template
    └── recovery-protocol.md ← Post-compaction recovery
```

## Highlights

### forbidden.md — Notice How Specific It Is

Not "avoid unsafe patterns" but:
- "`any` type — use `unknown` with type guards at `src/lib/api-client.ts` boundaries"
- "`console.log` — use `src/lib/logger.ts` which integrates with error tracking"
- "Default exports — use named exports for better refactoring (enforced by eslint)"

### architecture.md — Real Boundaries

Not "separate concerns" but:
- "Server Components in `app/` MUST NOT import from `src/components/client/`"
- "Database access ONLY through `src/lib/db/` — never import Prisma directly in route handlers"
- "API routes in `app/api/` delegate to `src/services/` — no business logic in route files"

### shield/invariants.md — Compaction-Proof

The top 12 rules that survive context compaction, formatted for quick re-reading:
- Build commands: `pnpm test`, `pnpm lint`, `pnpm typecheck`
- Architecture: no direct Prisma imports outside `src/lib/db/`
- Security: all API routes require auth middleware
- Testing: no mocking of Prisma — use test database
