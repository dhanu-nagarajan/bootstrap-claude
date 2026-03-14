# Shield File Specifications

This document specifies the exact structure and content requirements for each file generated in `.claude/shield/`. Every file must be populated with the actual rules and patterns discovered during project analysis — never with generic placeholder advice.

---

## File 1: `invariants.md`

**Purpose:** The single most important file after compaction. Contains the project's critical rules in a format designed to be re-read and re-internalized in under 60 seconds.

**Token budget:** MUST stay under 2000 tokens (~150 lines). This is a hard constraint — if it's too long, Claude won't re-read it fully after compaction, defeating the purpose.

### Structure

```markdown
# Project Invariants

> If you are reading this after context compaction, STOP what you are doing.
> Read this entire file before resuming any work.
> Do NOT assume you remember the rules correctly — re-read them.

## Critical Commands

- **Build:** `{actual build command}`
- **Test:** `{actual test command}`
- **Lint:** `{actual lint command}`
- **Type check:** `{actual type check command}`

Only include commands that actually exist in this project. If there is no lint command, omit it.

## Hard Constraints

These rules are non-negotiable. Violating any of them produces bugs, security issues, or data loss.

1. {HARD CONSTRAINT from project's .claude/ files}
2. {HARD CONSTRAINT from project's .claude/ files}
3. ...up to 5 hard constraints

Format each as a single, direct sentence. Not "Consider doing X" — instead "Always do X" or "Never do Y".

## Important Rules

These rules maintain code quality and consistency. Violating them creates technical debt.

6. {IMPORTANT rule from project's .claude/ files}
7. {IMPORTANT rule from project's .claude/ files}
8. ...up to 10 additional rules

## Forbidden Patterns

Do NOT do any of the following:

- {forbidden pattern from .claude/standards/forbidden.md}
- {forbidden pattern}
- ...all forbidden patterns from the project

## Architecture Boundaries

{Brief description of the project's architecture layers and dependency rules, extracted from .claude/standards/architecture.md. Keep to 3-5 bullet points maximum.}

---

## Verification Checklist

Before resuming work after compaction, confirm you understand:

- [ ] The build/test commands listed above
- [ ] All hard constraints (re-read Section 2 if unsure)
- [ ] All forbidden patterns (re-read Section 4 if unsure)
- [ ] The architecture boundaries (re-read Section 5 if unsure)

If you cannot check all boxes from memory, re-read the relevant section.
```

### Content Rules

1. **Extract, don't invent.** Every rule must trace back to an actual `.claude/` file. If the project has no forbidden patterns file, the Forbidden Patterns section should note that and list any constraints found in other files.

2. **Prioritize by damage potential.** Hard Constraints are rules where violation causes:
   - Runtime errors or crashes
   - Security vulnerabilities
   - Data loss or corruption
   - Breaking changes to public APIs
   - CI/CD pipeline failures

3. **Use the project's actual terminology.** If the project calls its data layer "repositories", use "repositories" — not "data access layer" or "DAL".

4. **Include actual file paths.** Instead of "don't modify the config", write "Never modify `src/config/production.ts` directly — use environment variables."

5. **One rule, one line.** No multi-paragraph explanations. If a rule needs explanation, it's too complex for the invariants file — simplify it.

---

## File 2: `session-state.md`

**Purpose:** A living document that Claude updates during active work to track progress, discoveries, and failed approaches. After compaction, this is how Claude knows what it was doing and what has already been tried.

**Update frequency:** Claude should update this file at every significant milestone — completing a step, discovering something important, or when an approach fails.

### Structure

```markdown
# Session State

> Last updated: {timestamp}
>
> This file tracks the current working state. After context compaction,
> read this to understand what you were doing and what has been tried.

## Current Task

**Goal:** {one-line description of what we're trying to accomplish}

**Context:** {why we're doing this — the user request or issue being addressed}

**Acceptance criteria:**
- {criterion 1}
- {criterion 2}

## Approach

**Current strategy:** {what approach we're taking and why}

**Key decisions made:**
- {decision 1 and rationale}
- {decision 2 and rationale}

## Completed Steps

- [x] {step 1 — what was done and outcome}
- [x] {step 2 — what was done and outcome}
- [ ] {step 3 — next step to do}
- [ ] {step 4 — future step}

## Failed Approaches

> CRITICAL: Do NOT retry these. They were already attempted and failed.

### {Failed approach 1 name}
- **What was tried:** {description}
- **Why it failed:** {specific reason}
- **Evidence:** {error message, test output, or observation}

### {Failed approach 2 name}
- **What was tried:** {description}
- **Why it failed:** {specific reason}
- **Evidence:** {error message, test output, or observation}

## Key Discoveries

Findings during this session that affect the work:

- {discovery 1 — e.g., "The auth middleware expects JWT in cookie, not header"}
- {discovery 2}

## Working Files

Files currently being created or modified:

| File | Status | Notes |
|------|--------|-------|
| {path/to/file} | {creating/modifying/done} | {what changed} |
```

### Content Rules

1. **Template ships empty.** When first generated, section bodies contain only the template field names in curly braces. Claude fills them in when starting actual work.

2. **Failed Approaches is the most critical section.** This section prevents the #1 post-compaction problem: Claude retrying the same failed approach. Every failed attempt MUST be logged here with the specific reason it failed.

3. **Completed Steps uses checkboxes.** Checked items are done. Unchecked items are the remaining plan. After compaction, Claude can see exactly where to resume.

4. **Timestamps matter.** The "Last updated" timestamp helps Claude gauge how stale the information might be.

5. **Working Files prevents conflicts.** After compaction, Claude might not remember which files it was in the middle of editing. This table prevents starting edits on the wrong file or losing track of multi-file changes.

---

## File 3: `recovery-protocol.md`

**Purpose:** Step-by-step instructions that Claude follows immediately after detecting a compaction event. Must be short, direct, and impossible to misinterpret.

**Length constraint:** Under 50 lines. Must be readable in under 30 seconds.

### Structure

```markdown
# Post-Compaction Recovery Protocol

**When to use this:** You are reading this because your context was compacted.
Your previous instructions may have been summarized. Critical details may be lost.
Do NOT trust your memory. Follow these steps exactly.

---

## Step 1: STOP

Do not continue any in-progress work. Do not write any code. Do not run any commands.
You need to re-establish your constraints and context first.

## Step 2: Re-read Invariants

```
Read file: .claude/shield/invariants.md
```

This file contains the project's critical rules, forbidden patterns, and architecture
boundaries. Re-internalize all of them before proceeding.

## Step 3: Re-read Session State

```
Read file: .claude/shield/session-state.md
```

This file contains what you were working on, what you've completed, what approaches
failed, and which files you were modifying. Do NOT retry failed approaches.

## Step 4: Re-read Project Configuration

```
Read file: CLAUDE.md
```

Re-load the project's routing table, build commands, and operational configuration.
If CLAUDE.md references additional `.claude/` files relevant to your current task,
read those too.

## Step 5: Verify Understanding

Before resuming, state back to the user:
1. **Current task:** What you are working on
2. **Current approach:** How you are implementing it
3. **Top 3 constraints:** The most critical rules for this task
4. **Last completed step:** Where you left off
5. **Failed approaches:** What has already been tried and must not be retried

## Step 6: Resume

Continue from the last completed step documented in session-state.md.
Do NOT restart from the beginning. Do NOT redo completed work.
Update session-state.md as you make progress.

---

**Remember:** It is better to spend 60 seconds re-reading than to spend
30 minutes redoing work or violating constraints you forgot about.
```

### Content Rules

1. **No customization needed.** Unlike invariants.md and session-state.md, the recovery protocol is the same for every project. It references the other shield files by path.

2. **Imperative mood only.** "Read this file" not "You should consider reading this file."

3. **Bold the critical words.** The protocol must be scannable — bold key actions and file paths.

4. **The verification step is non-optional.** Claude must state back its understanding before resuming. This forces re-processing of the re-read information rather than just skimming.
