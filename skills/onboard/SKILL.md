---
name: onboard
description: >-
  This skill should be used when the user asks to "onboard me", "walk me through
  the codebase", "I'm new to this project", "explain the codebase", "give me
  a tour", "help me understand this project", "codebase overview",
  or invokes /onboard. It provides an interactive, layered walkthrough of the
  project using the .claude/context/ files.
---

# Onboard: Interactive Codebase Walkthrough

Guide a new developer through the project in digestible layers, starting with purpose and zooming into implementation. This is a conversation, not a document dump — adapt pacing and depth to the developer's experience level and questions.

**Quality bar:** After the walkthrough, the developer should be able to make their first meaningful contribution without asking "where does this go?" or "how do I run this?"

## Execution

### Step 1: Prerequisites Check

Verify `.claude/context/` exists and contains at least `domain.md`.

If `.claude/context/` does not exist:
- Tell the user: "No `.claude/context/` directory found. Run `/bootstrap` first to generate project context files."
- As a fallback, offer a lighter walkthrough using only `README.md`, `CLAUDE.md`, and directory exploration — but note it will be less comprehensive.

### Step 2: Load Context

Read all available context and standards files:

- `.claude/context/domain.md` — Business domain and purpose
- `.claude/context/decisions.md` — Architecture decisions and rationale
- `.claude/context/glossary.md` — Project-specific terminology
- `.claude/standards/architecture.md` — System structure and boundaries
- `.claude/standards/language.md` — Coding conventions
- `.claude/standards/forbidden.md` — Anti-patterns to avoid
- `.claude/checklists/pre-commit.md` — Pre-commit verification steps
- `CLAUDE.md` — Project overview and commands
- `README.md` — Public project description

Also read the onboarding specification for guidance on pacing and depth:

```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/onboard/references/onboard-spec.md
```

Not all files will exist — adapt the walkthrough to available material.

### Step 3: Gauge Experience Level

Before diving in, ask the developer one question: "What's your experience level with [primary language/framework]? And have you worked on similar [project type] projects before?"

Use the answer to calibrate:
- **Senior + domain experience** — Move fast, focus on what's unique about THIS project
- **Senior + new domain** — Normal pace on purpose/domain, fast on code patterns
- **Junior** — Slower pace, more examples, check understanding at each layer

### Step 4: Layer 1 — Purpose

Present the project's reason for existing:

- **What does this project do?** (from domain.md and README.md)
- **Who are the users?** (from domain.md)
- **What problem does it solve?** (from domain.md)
- **Key terminology** — Introduce 3-5 domain terms the developer will encounter everywhere (from glossary.md)

Keep this brief for experienced developers. Spend more time here for juniors or those new to the domain.

After presenting, ask: "Any questions about the project's purpose before we look at the architecture?"

### Step 5: Layer 2 — Architecture

Walk through how the code is organized:

- **High-level structure** — Run `ls` on the project root, explain what each top-level directory does (from architecture.md)
- **Module map** — Which modules exist, what each is responsible for, how they relate
- **Data flow** — Trace a typical request or operation through the system: entry point, processing, storage, response
- **Key files** — Identify 3-5 files the developer should read first to understand the system (the "start here" files)

Use the actual directory tree — run `ls` on key directories and explain what the developer sees. This grounds the architecture in reality, not abstraction.

After presenting, ask: "Want me to go deeper into any specific module or trace another flow?"

### Step 6: Layer 3 — Decisions

Explain WHY the project is built this way:

- **Tech stack choices** — Why this language, framework, database? (from decisions.md)
- **Architecture decisions** — Key ADRs and their rationale (from decisions.md)
- **Constraints** — What external factors shaped the design? (regulatory, performance, team size)
- **Trade-offs acknowledged** — What was deliberately sacrificed and why?

This layer turns "how it works" into "why it works this way." Skip or abbreviate for developers who just need to ship code.

### Step 7: Layer 4 — Contributing

Give the developer everything they need to make their first change:

- **Setup commands** — How to install, build, and run locally (from CLAUDE.md)
- **Test commands** — How to run tests, what framework is used (from CLAUDE.md, testing.md)
- **Code patterns** — Show 1-2 examples of the project's common patterns (from standards)
- **Where new code goes** — Module placement rules (from architecture.md)
- **What to avoid** — Top 5 forbidden patterns (from forbidden.md)
- **Pre-commit checklist** — What to verify before pushing (from pre-commit.md)

Offer: "Want me to walk through adding a specific feature as a concrete example?"

### Step 8: Interactive Q&A

After the walkthrough, shift to a mentor persona. If `.claude/personas/mentor.md` exists, read it and adopt that persona's characteristics.

In mentor mode:
- Answer follow-up questions about the codebase
- Offer to explore specific files or modules in detail
- Help the developer plan their first task
- Point to relevant standards when they ask "how should I..."
- Stay patient and thorough — the goal is developer confidence

Continue in this mode until the developer indicates they are ready to work independently.

## Quality Principles

1. **Conversation, not lecture** — Pause for questions, check understanding, adapt depth
2. **Concrete, not abstract** — Show actual files, actual directories, actual commands
3. **Layered, not flat** — Start high, zoom in on request — never dump everything at once
4. **Tailored to experience** — Skip what they already know, expand what they do not
5. **Empowering** — End with the developer feeling capable, not overwhelmed
