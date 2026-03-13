# bootstrap-claude

A Claude Code plugin that generates a complete `.claude/` engineering system tailored to any project. One command analyzes your entire codebase and produces 25+ files of project-specific protocols, standards, templates, and context.

## What it does

Run `/bootstrap` in any project and it will:

1. **Deep-analyze your codebase** — stack, directory structure, patterns, conventions, config, dependencies, git history
2. **Generate 25+ tailored files** across 6 directories in `.claude/`
3. **Update your `CLAUDE.md`** with a routing table that tells Claude which files to load based on task type

Every generated file is specific to YOUR project — no generic filler. Standards reference your actual code patterns, templates match your architecture, and checklists use your real commands.

## Generated structure

```
.claude/
├── protocols/    — Phased workflows for features, bug fixes, and retros
├── standards/    — Code quality rules derived from your codebase
├── templates/    — Scaffolding for patterns your project repeats
├── checklists/   — Verification gates (pre-commit, PR review, new module, incident)
├── personas/     — Interaction modes (architect, reviewer, debugger, optimizer, mentor)
└── context/      — Domain knowledge, Architecture Decision Records, glossary
```

## Installation

### 1. Add the marketplace

```
/plugin marketplace add dhanu-nagarajan/bootstrap-claude
```

### 2. Install the plugin

```
/plugin install bootstrap-claude@bootstrap-claude
```

## Usage

Navigate to any project and run:

```
/bootstrap
```

That's it. The skill analyzes the current project and generates everything.

## What gets generated

| Directory | Files | Purpose |
|-----------|-------|---------|
| `protocols/` | 4 | Phased workflows — core doctrine, feature requests, bug fixes, retrospectives |
| `standards/` | 7 | Architecture, language conventions, testing, error handling, performance, security, forbidden patterns |
| `templates/` | 2-4 | Project-specific scaffolding (e.g., new endpoint, new component, new test) |
| `checklists/` | 4 | Pre-commit, new module, PR review, incident response |
| `personas/` | 5 | Architect, code reviewer, optimizer, debugger, mentor |
| `context/` | 3 | Domain knowledge, ADRs inferred from codebase, project glossary |

## Requirements

- [Claude Code](https://claude.ai/code) CLI
