# bootstrap-claude

A Claude Code plugin that generates a complete `.claude/` engineering system tailored to any project.

## What it does

Run `/bootstrap` in any project and it will:

1. Analyze the codebase (stack, patterns, conventions, architecture)
2. Generate 25+ files across 6 directories in `.claude/`
3. Update `CLAUDE.md` with a routing table that tells Claude which files to load based on task type

## Generated structure

```
.claude/
├── protocols/    — Phased workflows (features, bugs, retros)
├── standards/    — Code quality rules derived from YOUR codebase
├── templates/    — Scaffolding for repeating patterns
├── checklists/   — Verification gates (pre-commit, PR review, etc.)
├── personas/     — Interaction modes (architect, reviewer, debugger, etc.)
└── context/      — Domain knowledge, ADRs, glossary
```

## Installation

```bash
claude --plugin-dir /path/to/bootstrap-claude
```

## Usage

```
/bootstrap
```

That's it. The skill analyzes the current project and generates everything.
