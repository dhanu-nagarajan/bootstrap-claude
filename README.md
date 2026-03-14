# bootstrap-claude

**Your Claude Code sessions keep forgetting your rules. This fixes that.**

Claude Code loses your project rules during context compaction. bootstrap-claude generates validated, project-specific rules AND protects them from being compacted away.

## The Problem

You write careful CLAUDE.md rules. Claude Code follows them — until context compaction silently summarizes them away. Then it writes `any` types, skips tests, and ignores your architecture. Sessions degrade from productive to destructive.

## The Fix

```
/install-plugin https://github.com/dhanu-nagarajan/bootstrap-claude
/bootstrap
```

30 seconds. Your codebase is analyzed, validated rules are generated, and compaction protection is active.

## What You Get

### Choose Your Tier

| Tier | Files | Time | What You Get |
|------|-------|------|-------------|
| `/bootstrap --lite` | 4 | ~30s | Shield + forbidden patterns. Proves value immediately. |
| `/bootstrap` | 14 | ~2min | + standards, checklists, context. **Default.** |
| `/bootstrap --full` | 25+ | ~5min | + protocols, templates, personas. Complete system. |

### Generated Output (standard tier)

```
.claude/
├── standards/
│   ├── forbidden.md       # Anti-patterns specific to YOUR codebase
│   ├── architecture.md    # Module boundaries and dependency rules
│   ├── language.md        # Naming conventions from YOUR code
│   ├── testing.md         # YOUR test framework and patterns
│   └── security.md        # Security patterns for YOUR stack
├── checklists/
│   ├── pre-commit.md      # YOUR actual build/test/lint commands
│   └── pr-review.md       # Review dimensions
├── context/
│   ├── decisions.md       # Inferred architecture decision records
│   └── glossary.md        # Project-specific terminology
└── shield/
    ├── invariants.md      # Top rules, compaction-proof format
    ├── session-state.md   # Live session tracking
    └── recovery-protocol.md  # Post-compaction recovery
```

Every reference is validated against your actual codebase. File paths, commands, function names — all verified. A confidence score tells you how grounded the output is.

**Want to see real output?** Check the [showcase/](showcase/) directory for generated examples on Next.js, FastAPI, and Rust projects.

## How Compaction Protection Works

```
Normal session:     Rules active → compaction → rules gone → bad code
With /shield:       Rules active → compaction → recovery triggers →
                    re-read invariants → re-read session state →
                    verify constraints → resume from last checkpoint
```

Shield generates:
- **`invariants.md`** — Your top 10-15 critical rules in a redundant format designed to survive compaction
- **`session-state.md`** — Living document tracking current task, completed steps, and failed approaches
- **`recovery-protocol.md`** — Step-by-step procedure Claude follows after compaction to restore context

## All 9 Skills

| Skill | What it does | When to use it |
|-------|-------------|----------------|
| `/bootstrap` | Generate validated rules from your codebase | First time setup |
| `/shield` | Protect rules from context compaction | Every session |
| `/audit` | Check codebase against your rules + compliance score | Before PRs, in CI |
| `/doctor` | Detect when rules have drifted from codebase | Weekly |
| `/update` | Refresh rules after codebase changes | After refactors |
| `/export` | Convert rules to Cursor/Copilot/Aider/Cline/AGENTS.md | Team alignment |
| `/onboard` | Interactive codebase walkthrough | New contributors |
| `/roast` | Shareable codebase quality report with scores | Anytime |
| `/explain` | Generate architecture narrative document | Documentation |

## Quick Comparison

| | Hand-written CLAUDE.md | bootstrap-claude v3 |
|---|---|---|
| Validated against codebase | No | Yes, every reference verified |
| Survives compaction | No | Yes, with /shield |
| Stays current | Manual effort | /doctor + /update lifecycle |
| Works with other AI tools | Rewrite it yourself | /export to 5 tools |
| Compliance scoring | No | /audit with CI integration |
| Time to set up | Hours | 30 seconds |

## Installation

### From GitHub

```
/install-plugin https://github.com/dhanu-nagarajan/bootstrap-claude
```

### Local Development

```
claude --plugin-dir /path/to/bootstrap-claude
```

## Configuration

### Basic Usage

Navigate to any project and run:

```
/bootstrap
```

### Flags

```
/bootstrap --lite              # Minimal: shield + forbidden (4 files)
/bootstrap                     # Standard: full standards (14 files, default)
/bootstrap --full              # Complete: everything (25+ files)
/bootstrap --dry-run           # Preview without writing files
/bootstrap --only standards    # Generate only specific categories
/bootstrap --profile fintech   # Apply industry profile
```

### Project Configuration

Create `.bootstrap-config.yaml` in your project root:

```yaml
version: 3
tier: standard
profile: fintech              # fintech | healthcare | startup | open-source
specs:
  include:
    - ./.bootstrap/specs/graphql.md  # Custom specs
  exclude:
    - personas                        # Skip categories
export:
  targets: [cursor, copilot]
  auto_export: true
shield:
  auto_refresh: true
  custom_invariants:
    - "All API endpoints require authentication"
```

## Cross-Tool Export

One source of truth, consistent standards everywhere:

```
/export cursor    →  .cursor/rules/*.mdc
/export copilot   →  .github/copilot-instructions.md
/export aider     →  .aider.conventions.md
/export cline     →  .clinerules
/export agents    →  AGENTS.md
```

## CI/CD Integration

### GitHub Action

```yaml
- name: Run audit
  uses: dhanu-nagarajan/bootstrap-claude/actions/audit@v3
  with:
    claude-api-key: ${{ secrets.ANTHROPIC_API_KEY }}
    threshold: 80
```

### Compliance Badge

After running `/audit`, add to your README:

```markdown
![bootstrap-claude compliance](https://img.shields.io/badge/bootstrap--claude-94%25_compliant-brightgreen)
```

## Extensibility

bootstrap-claude uses a registry system. Add your own:

- **Language examples** — `registry/examples/EXAMPLE-TEMPLATE.md`
- **Industry profiles** — `registry/profiles/PROFILE-TEMPLATE.md`
- **Export formats** — `skills/export/references/formats/FORMAT-TEMPLATE.md`

See [CONTRIBUTING.md](CONTRIBUTING.md) for details.

## Supported Languages

**Calibrated examples:** TypeScript, Python, Rust, Go, Java

**Works with any language** — analysis adapts to whatever it finds. Calibrated languages get more specific output.

## Staying Up to Date

```
/update plugin     # Update the plugin itself
/update            # Refresh .claude/ files to match codebase changes
```

## Roadmap

- [ ] More language calibrations (C#, Swift, Kotlin, PHP, Ruby)
- [ ] Monorepo support (per-package standards with inheritance)
- [ ] Community spec marketplace
- [ ] Cross-session memory bridge
- [ ] Additional export formats (Windsurf, Zed, Continue.dev)

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Easy first contributions:
- Add a language example (copy `registry/examples/EXAMPLE-TEMPLATE.md`)
- Add an export format (copy `skills/export/references/formats/FORMAT-TEMPLATE.md`)
- Add an industry profile (copy `registry/profiles/PROFILE-TEMPLATE.md`)

## License

[MIT](LICENSE)
