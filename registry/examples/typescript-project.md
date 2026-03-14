# Reference Example: TypeScript Project Output

This shows what high-quality, project-specific output looks like for a TypeScript project. Use as a calibration reference — your output should be THIS specific, never more generic.

## Example: standards/language.md (for a Next.js + TypeScript project)

```markdown
# TypeScript Conventions

## Naming
- **Files**: kebab-case for all files (`user-profile.tsx`, `api-client.ts`)
- **Components**: PascalCase matching filename (`UserProfile` in `user-profile.tsx`)
- **Functions**: camelCase, verb-first (`getUserById`, `handleSubmit`, `parseResponse`)
- **Constants**: SCREAMING_SNAKE for true constants (`MAX_RETRY_COUNT`), camelCase for derived values
- **Types/Interfaces**: PascalCase, no `I` prefix (`UserProfile` not `IUserProfile`)
- **Enums**: PascalCase members (`Status.Active` not `Status.ACTIVE`)
- **Hooks**: `use` prefix always (`useAuth`, `useDebounce`)
- **Event handlers**: `handle` prefix in components (`handleClick`), `on` prefix in props (`onClick`)

## Type Discipline
- Strict mode enabled (tsconfig: `strict: true`, `noUncheckedIndexedAccess: true`)
- Prefer `interface` for object shapes, `type` for unions/intersections/mapped types
- Use `unknown` over `any` — narrow with type guards
- Zod schemas at API boundaries, infer types with `z.infer<typeof schema>`
- Discriminated unions for state machines: `{ status: 'loading' } | { status: 'error'; error: Error } | { status: 'success'; data: T }`

## Patterns (from codebase analysis)

### Data Fetching
```typescript
// Pattern found in src/lib/api.ts — all API calls follow this shape
export async function fetchUser(id: string): Promise<Result<User>> {
  const response = await fetch(`/api/users/${id}`);
  if (!response.ok) {
    return { success: false, error: await parseApiError(response) };
  }
  return { success: true, data: userSchema.parse(await response.json()) };
}
```

### Error Handling
```typescript
// Pattern found in src/lib/errors.ts — custom error hierarchy
export class AppError extends Error {
  constructor(
    message: string,
    public readonly code: ErrorCode,
    public readonly statusCode: number = 500,
    public readonly context?: Record<string, unknown>
  ) {
    super(message);
  }
}
```

### Import Organization
```typescript
// 1. Node/framework imports
import { useState, useCallback } from 'react';
import { NextRequest, NextResponse } from 'next/server';

// 2. Third-party libraries
import { z } from 'zod';
import { clsx } from 'clsx';

// 3. Internal absolute imports (by layer)
import { db } from '@/lib/database';
import { UserProfile } from '@/components/user-profile';

// 4. Relative imports (same module)
import { validateInput } from './validation';
import type { FormState } from './types';
```
```

## Example: standards/forbidden.md (for a TypeScript project)

```markdown
# Forbidden Patterns

These trigger immediate rejection in code review. No exceptions without ADR.

- `any` type — Use `unknown` + type narrowing; `any` disables the type system
- `// @ts-ignore` without explanation — Fix the type error; if truly unfixable, use `@ts-expect-error` with reason
- `console.log` in committed code — Use the logger from `src/lib/logger.ts`
- `as` type assertion without validation — Unsafe cast; use Zod parse or type guard
- `innerHTML` or `dangerouslySetInnerHTML` — XSS vector; use text content or sanitized markdown
- `eval()` or `new Function()` — Code injection; never acceptable
- `var` declarations — Use `const` (preferred) or `let`
- Non-null assertion `!` on user input — Validate instead of asserting
- `setTimeout` for sequencing — Use proper async/await patterns
- `export default` — Named exports enable better tree-shaking and refactoring
- Barrel files (`index.ts` re-exporting everything) — Breaks tree-shaking; import directly
- Mutable global state — Use React context, Zustand store, or module-scoped constants
- String concatenation for SQL/HTML — Use parameterized queries and template components
- `Promise` constructor for wrapping async — Unnecessary when using async/await
- Synchronous file I/O in API routes — Use `fs/promises` to avoid blocking the event loop
```

## Example: checklists/pre-commit.md (for a Next.js project)

```markdown
## Pre-Commit Checklist

### Automated (must all pass)
- [ ] `npm run typecheck` — TypeScript compilation with strict mode
- [ ] `npm run lint` — ESLint with project rules
- [ ] `npm run test` — Vitest test suite
- [ ] `npm run build` — Next.js production build succeeds

### Manual Verification
- [ ] No `console.log`, `debugger`, or `TODO: HACK` in diff
- [ ] No `.env` values, API keys, or tokens in diff
- [ ] No unrelated changes bundled in this commit
- [ ] New API routes have Zod input validation
- [ ] New components have loading and error states
- [ ] Database migrations are reversible
- [ ] Commit message: imperative mood, explains WHY
```
