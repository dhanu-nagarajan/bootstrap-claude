# Convention Extraction Protocol

How to extract actual coding conventions from source files rather than inventing them.

---

## Methodology

### File Selection

Pick 3-5 representative source files using these criteria:
1. **Different directories** — don't pick 3 files from the same folder
2. **Recent activity** — prefer files with recent git commits
3. **Non-trivial** — skip config files, type declarations, index files
4. **Core logic** — pick files that contain business logic or main functionality

### What to Extract

For each file, note:

#### Naming Patterns
```
Functions:    camelCase / snake_case / PascalCase
Variables:    camelCase / snake_case / SCREAMING_SNAKE
Files:        kebab-case / camelCase / PascalCase / snake_case
Classes:      PascalCase (usually universal)
Constants:    SCREAMING_SNAKE / camelCase
Interfaces:   IPrefix / no prefix / Suffix
Types:        TSuffix / no suffix
Enums:        PascalCase / SCREAMING_SNAKE members
```

Record 2-3 actual examples with file references for each pattern found.

#### Import Organization
```
Groups:       external → internal → relative? Or ungrouped?
Sorting:      alphabetical within groups? By type?
Aliasing:     @/ paths? ~/? Relative only?
Barrel files: index.ts re-exports? Or direct imports?
Type imports: separate `import type` statements?
```

#### Error Handling
```
Pattern:      try/catch / Result type / error boundaries / .catch()
Custom errors: Yes (class hierarchy) / No (string messages)
Logging:      structured / console / framework logger
Recovery:     retry / fallback / propagate / swallow
```

#### Code Structure
```
File length:     typical lines per file
Function length: typical lines per function
Nesting:         max depth observed
Comments:        frequency and style
Exports:         named / default / mixed
```

### Cross-Referencing

After extracting from individual files, check for consistency:
- If 4/5 files use camelCase functions → that's the convention
- If 3/5 files use one pattern and 2/5 use another → note the inconsistency
- If a linter config enforces a rule → that overrides observed patterns

### Linter Configuration

Check linter configs for enforced conventions:
- `.eslintrc*` / `eslint.config.*` — JavaScript/TypeScript rules
- `ruff.toml` / `pyproject.toml [tool.ruff]` — Python rules
- `clippy.toml` / `.clippy.conf` — Rust rules
- `.golangci.yml` — Go rules

Linter rules are more authoritative than observed patterns (code might violate its own lint rules).

## Output Format

```
Naming Conventions:
  Functions: camelCase (evidence: getUserById in src/api/users.ts:15,
    createOrder in src/api/orders.ts:23, validateInput in src/lib/validation.ts:8)
  Files: kebab-case (evidence: user-service.ts, order-handler.ts, auth-middleware.ts)
  Components: PascalCase (evidence: UserProfile.tsx, OrderList.tsx, AuthForm.tsx)

Import Style:
  Organization: grouped (external → @/ internal → relative)
  Evidence: consistent across src/api/users.ts, src/components/UserProfile.tsx

Error Handling:
  Pattern: custom error classes extending AppError
  Evidence: src/lib/errors.ts defines hierarchy, used in src/api/*.ts

Enforced by linter:
  - no-unused-vars (eslint)
  - @typescript-eslint/no-explicit-any (eslint)
  - prefer-const (eslint)
```
