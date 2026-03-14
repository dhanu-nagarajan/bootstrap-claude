# bootstrap-claude

The engineering immune system for Claude Code. One plugin, 7 skills — generates a validated `.claude/` engineering system with compaction-resilient session protection, standards enforcement, cross-tool export, and incremental maintenance.

## The Problem

Claude Code's #1 pain point: **context compaction silently destroys your rules mid-session**. Your carefully written CLAUDE.md instructions get summarized away. Claude reads your rules, quotes them back, then violates them. Sessions degrade from productive to destructive after 5-6 hours.

Beyond compaction, every new session cold-starts — no memory of your project's architecture, conventions, or past decisions. Standards are advisory prose that nothing enforces. And when your codebase evolves, your `.claude/` files go stale with nobody maintaining them.

## The Solution: 7 Integrated Skills

### `/bootstrap` — Generate Everything

Deep-analyzes your codebase (stack, patterns, conventions, git history, dependencies) and generates 25+ validated files across 7 directories:

```
.claude/
├── protocols/    — Phased workflows for features, bug fixes, retros
├── standards/    — Code quality rules derived from YOUR codebase
├── templates/    — Scaffolding for patterns your project repeats
├── checklists/   — Verification gates with your actual commands
├── personas/     — Interaction modes with project-specific context
├── context/      — Domain knowledge, ADRs, glossary
└── shield/       — Compaction-resilient session protection
```

Every reference is validated against the actual codebase. File paths, commands, function names — all verified. A confidence score tells you exactly how grounded the output is.

### `/shield` — Survive Context Compaction

The killer feature no competitor has. Generates:
- **`invariants.md`** — Your top 10-15 critical rules in a redundant format designed to survive compaction
- **`session-state.md`** — Living document tracking current task, approach, completed steps, and failed approaches
- **`recovery-protocol.md`** — Step-by-step procedure Claude follows after compaction to restore context

Injects a recovery section into CLAUDE.md that forces Claude to re-read its constraints after any compaction event.

### `/audit` — Enforce Standards

Scans your codebase against the generated standards and produces a compliance report:
- Forbidden pattern violations with file:line references
- Architecture boundary violations
- Naming convention drift
- Test coverage gaps
- Compliance score per standard

### `/doctor` — Detect Drift

Checks whether your `.claude/` files still match reality:
- File paths that no longer exist
- Commands that were renamed or removed
- New modules not covered by architecture standards
- New dependencies not reflected in documentation

### `/update` — Incremental Maintenance & Self-Update

**Two modes in one command:**

**Update your standards** (default) — Runs `/doctor` internally, then surgically updates only the drifted `.claude/` files:
- Preserves your manual edits (marked with `<!-- user-edited -->`)
- Shows diffs before applying changes
- Updates the shield invariants with any new rules
- Tracks state in `.bootstrap-state.json`

**Update the plugin itself** — Run `/update plugin` to check for and install the latest version:
```
/update plugin
```
```
🔄 Update available: v2.0.0 → v2.1.0
   Pulling latest changes... ✅
   bootstrap-claude updated to v2.1.0
```

The plugin also checks for updates automatically when you run `/bootstrap` — you'll see a notice if a newer version is available.

### `/export` — Cross-Tool Standards

Exports your `.claude/` standards to every major AI coding tool:
- **Cursor** → `.cursor/rules/*.mdc` with glob-based conditional loading
- **GitHub Copilot** → `.github/copilot-instructions.md`
- **Aider** → `.aider.conventions.md`
- **Cline** → `.clinerules`
- **Multi-agent** → `AGENTS.md`

One source of truth, consistent standards everywhere.

### `/onboard` — Codebase Walkthrough

Interactive, layered tour of the project for new team members:
1. What does this project do? (purpose and domain)
2. How is it structured? (architecture with file exploration)
3. Why was it built this way? (decisions and trade-offs)
4. How do I contribute? (commands, patterns, conventions)

## Installation

### From Marketplace

```
/plugin marketplace add dhanu-nagarajan/bootstrap-claude
/plugin install bootstrap-claude@bootstrap-claude
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

### Organization Profiles

Create `.bootstrap-config.yaml` in your project root for team-level control:

```yaml
version: 2
profile: fintech              # fintech | healthcare | startup | open-source
overrides:
  forbidden:
    - "eval() in any context"
  security:
    compliance: [SOC2, PCI-DSS]
export:
  targets: [cursor, copilot]  # Auto-export on bootstrap
shield:
  invariants:
    - "All API endpoints require authentication"
```

Available profiles add industry-specific standards, forbidden patterns, and compliance checklists.

## Staying Up to Date

The plugin automatically checks for updates when you run `/bootstrap`. If a newer version exists, you'll see a notice:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔄 bootstrap-claude update available: v2.0.0 → v2.1.0
   Run /update plugin to upgrade
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

To update:

```
/update plugin
```

After updating the plugin, run `/bootstrap` again in your projects to regenerate `.claude/` files with the improved specs. Or run `/update` (without "plugin") to incrementally refresh only what changed.

## What Makes This Enterprise-Grade

| Feature | Free Tools | bootstrap-claude v2 |
|---------|-----------|---------------------|
| Standards generation | Generic advice | Validated against actual codebase |
| Compaction resilience | None | Shield system with recovery protocol |
| Maintenance | Manual | `/doctor` + `/update` lifecycle |
| Cross-tool support | Single tool | Export to 5 AI coding tools |
| Compliance | None | Industry profiles (fintech, healthcare) |
| Onboarding | None | Interactive codebase walkthrough |
| Validation | None | Every reference verified, confidence scored |

## Requirements

- [Claude Code](https://claude.ai/code) CLI
