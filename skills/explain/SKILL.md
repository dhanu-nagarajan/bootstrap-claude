---
name: explain
description: >-
  This skill should be used when the user asks to "explain this codebase",
  "generate architecture doc", "write a system overview", "how does this
  project work", "create technical documentation", "explain the architecture",
  "document this project", or invokes /explain. It generates a readable,
  narrative-style architecture document with diagrams.
---

# Explain: Architecture Narrative Generator

Generate a readable, narrative-style architecture document that explains how this codebase works. Not a dry spec — a document a senior engineer would enjoy reading on their first day.

**Quality bar:** Every claim references actual files. Every diagram maps to real modules. The document should make a new team member productive within 30 minutes of reading it.

## Execution

### Step 1: Deep Analysis

Read the shared analysis protocol:

```
Read file: ${CLAUDE_PLUGIN_ROOT}/core/analysis/codebase-analyzer.md
```

Perform full codebase analysis with extra focus on:
- Entry points and request flow (trace a typical operation)
- Data model and persistence layer
- Module boundaries and dependency direction
- External integrations and APIs
- Key abstractions and design patterns
- Configuration and environment setup

### Step 2: Trace Request Flow

Pick the most common operation in the project (e.g., "user creates a resource", "CLI processes a command", "request hits an API endpoint") and trace it end-to-end through the code:

1. Where does the request enter? (HTTP handler, CLI parser, event listener)
2. What validates it? (middleware, validation layer)
3. What processes it? (service/business logic layer)
4. What persists it? (database, file system, external API)
5. What does it return? (response format, error format)

Reference specific files at each step.

### Step 3: Generate Architecture Document

Write `ARCHITECTURE.md` in the project root:

```markdown
# Architecture: {project-name}

> {One sentence: what this project is and what it does}

## The Big Picture

```
{ASCII diagram showing major components and their relationships}
{Use boxes, arrows, labels — keep it simple and accurate}
```

## How a {Typical Operation} Works

{Narrative walkthrough of one request/operation flowing through the system}

1. **Entry** — {what happens first, which file, why}
2. **Validation** — {what gets checked, where}
3. **Processing** — {core logic, which service/module}
4. **Persistence** — {how data is stored/retrieved}
5. **Response** — {what comes back, format}

## Key Abstractions

### {Abstraction 1} (`{file-path}`)
{What it is, why it exists, how it's used}

### {Abstraction 2} (`{file-path}`)
{What it is, why it exists, how it's used}

### {Abstraction 3} (`{file-path}`)
{What it is, why it exists, how it's used}

## Where Things Live

```
{directory-name}/
├── {dir}/        # {purpose}
├── {dir}/        # {purpose}
├── {dir}/        # {purpose}
└── {file}        # {purpose}
```

## Design Decisions

### Why {Decision 1}?
{Context, options considered, what was chosen, why, tradeoffs}

### Why {Decision 2}?
{Context, options considered, what was chosen, why, tradeoffs}

## The Dragons

{Known complexity, tech debt, areas that are hard to work with, with file references}

- **{Area}** (`{file-path}`) — {what's tricky and why}
- **{Area}** (`{file-path}`) — {what's tricky and why}

## Getting Oriented

If you're new, start by reading these files in order:
1. `{entry-point}` — the main entry point
2. `{core-model}` — the primary data model
3. `{typical-handler}` — a representative request handler
4. `{config}` — how configuration works
5. `{test-example}` — how tests are structured
```

### Step 4: Validate

Read the shared validation protocol:

```
Read file: ${CLAUDE_PLUGIN_ROOT}/core/validation/reference-validator.md
```

Verify all file references exist. Flag any that couldn't be verified.

### Step 5: Present

Show the generated document to the user. Report:
- Number of file references (all verified)
- Suggested reading order for new team members
- Offer to deep-dive into any section

## Quality Principles

1. **Narrative over specification** — Tell a story, don't list facts
2. **Concrete over abstract** — Reference specific files, not concepts
3. **Honest about complexity** — The "Dragons" section earns trust
4. **Opinionated about reading order** — Guide the reader
5. **Diagram accuracy** — ASCII diagrams must map to actual modules

**The test:** Would a senior engineer joining the team find this more useful than exploring the codebase randomly?

## Additional Resources

### Reference Files
- **`${CLAUDE_PLUGIN_ROOT}/core/analysis/codebase-analyzer.md`** — Shared analysis protocol
- **`${CLAUDE_PLUGIN_ROOT}/core/validation/reference-validator.md`** — Reference validation
- **`references/explain-spec.md`** — Additional generation guidance
