# Standardized Progress Output

All skills MUST use this format for user-facing progress output.

---

## Format

### Step Progress

```
{Verb}ing {noun}...
  ✓ {item description}
  ✓ {item description}
  ✗ {item description} ({reason})
  ⚠ {item description} ({warning})
```

### Symbols

| Symbol | Meaning |
|--------|---------|
| ✓ | Success / completed / verified |
| ✗ | Failed / error / not found |
| ⚠ | Warning / needs attention / partial |
| → | Suggestion / next step |

### Example: Bootstrap Progress

```
Analyzing codebase...
  ✓ Language: TypeScript + React (Next.js 14)
  ✓ Package manager: pnpm
  ✓ Test framework: Vitest + Testing Library
  ✓ Conventions: camelCase functions, PascalCase components
  ✓ Architecture: 4 layers (app/, lib/, components/, api/)
  ✓ Dependencies: 47 production, 23 dev

Generating standards...
  ✓ forbidden.md      8 anti-patterns verified
  ✓ architecture.md   4 boundaries, 12 references verified
  ✓ language.md       23 conventions extracted
  ✓ testing.md        test commands verified
  ✓ security.md       6 patterns verified

Generating shield...
  ✓ invariants.md     12 rules, 142 tokens
  ✓ session-state.md  template ready
  ✓ recovery-protocol.md  6 steps

Validating...
  ✓ 127/135 references verified (94% confidence)
  ⚠ 8 unverified references (see below)

Done! 12 files generated in .claude/
```

### Example: Error with Recovery

```
Analyzing codebase...
  ✓ Language: Python
  ✗ No package manifest found

  ⚠ Cannot detect dependencies without a package manifest.

  Options:
    1. Create a requirements.txt and re-run
    2. Continue anyway (some features will be limited)
    → Run: pip freeze > requirements.txt
```

## Summary Block

Every skill ends with a summary block:

```
Done! {summary of what was accomplished}

  {Key metric 1}: {value}
  {Key metric 2}: {value}

Next steps:
  /{skill}  {what it does}
  /{skill}  {what it does}
```

## Rules

1. Progress output must be real-time — show each step as it completes
2. Never show internal implementation details (spec file loading, prompt construction)
3. Always end with actionable next steps
4. Keep each line under 80 characters
5. Use consistent indentation (2 spaces for sub-items)
