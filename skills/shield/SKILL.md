---
name: shield
description: >-
  This skill should be used when the user asks to "protect session",
  "shield", "guard against compaction", "don't forget", "preserve context",
  "session protection", "guard rules", "shield my context", "protect my
  rules", "survive compaction", or invokes /shield. It generates a
  .claude/shield/ protection system that ensures critical project rules and
  session state survive context compaction events — preventing Claude from
  losing constraints, retrying failed approaches, or forgetting task progress.
---

# Shield: Context Compaction Protection System

Generate a `.claude/shield/` directory that protects critical project rules and working state from being lost during context compaction. The shield system uses redundant encoding, session state tracking, and a post-compaction recovery protocol to ensure Claude re-internalizes constraints before resuming work.

**The problem this solves:** When Claude Code's context window fills up, older messages get compacted (summarized). This destroys the specific rules, constraints, and working state that were established earlier in the conversation. Claude then violates project rules it was previously following, retries approaches that already failed, or restarts work that was already completed.

**Quality bar:** Every generated shield file must reference THIS project's actual rules, commands, and patterns. No generic advice. The invariants file must stay under 2000 tokens so it can be re-read quickly after compaction.

## Execution

### Step 1: Analyze Project Rules

Read all `.claude/` files to extract the project's critical rules and constraints. Specifically:

1. **CLAUDE.md** — The root configuration; extract build/test/lint commands, architecture overview, commit style
2. **`.claude/protocols/core.md`** — Core engineering protocol; extract mandatory workflow steps
3. **`.claude/standards/forbidden.md`** — Forbidden patterns; extract every banned practice
4. **`.claude/standards/architecture.md`** — Architecture boundaries; extract layer rules, dependency directions
5. **`.claude/standards/language.md`** — Language conventions; extract naming, typing, style rules
6. **Any other `.claude/` files** — Scan for additional hard constraints

Build a ranked list of rules by criticality:
- **Tier 1 (HARD CONSTRAINTS):** Rules that if violated cause bugs, security issues, or data loss
- **Tier 2 (IMPORTANT):** Rules that maintain code quality and consistency
- **Tier 3 (STYLE):** Preferences that affect readability but not correctness

Only Tier 1 and Tier 2 make it into the shield. Tier 3 can be re-derived from source files.

### Step 2: Generate Shield Files

Read the full specification from the shield spec:

```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/shield/references/shield-spec.md
```

That file contains the complete content specifications for every shield file. Follow it exactly, populating all content with the actual rules discovered in Step 1.

**Directory tree to generate:**

```
.claude/shield/
  invariants.md        — Top 10-15 critical rules, redundantly encoded
  session-state.md     — Working state template (to be updated during work)
  recovery-protocol.md — Step-by-step post-compaction recovery procedure
```

### Step 3: Inject Recovery Protocol

Read the recovery template:

```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/shield/references/recovery-template.md
```

Add the recovery protocol section to the project's `CLAUDE.md`:

- If CLAUDE.md has no recovery section, append it near the top (after any project overview, before detailed sections)
- If CLAUDE.md already has a recovery section, replace it with the updated version
- The section must be prominent — it needs to survive compaction summarization itself

### Step 4: Verify

1. Confirm all three shield files exist in `.claude/shield/`
2. Verify `invariants.md` is under 2000 tokens (approximately under 150 lines)
3. Verify `recovery-protocol.md` can be read in under 30 seconds (under 50 lines)
4. Verify CLAUDE.md contains the recovery protocol section
5. Spot-check that `invariants.md` references actual project rules, not generic advice
6. Report summary: what was created, rule count, any gaps needing manual review

## Quality Principles

1. **Project-specific** — Every rule in invariants.md must come from an actual `.claude/` file, not invented
2. **Redundantly encoded** — Critical rules appear in multiple formats (list, checklist, prose) to survive partial summarization
3. **Minimal** — Shield files must be short enough to re-read quickly; verbosity defeats the purpose
4. **Actionable** — Every recovery step tells Claude exactly what to do, not what to "consider"
5. **Self-reinforcing** — The recovery protocol itself must be written to survive compaction (bold, emoji markers, short sentences)

**The test:** If Claude's context gets compacted mid-task, can it recover to a productive state within 60 seconds by reading the shield files?

## Additional Resources

### Reference Files

The complete file content specifications are in:
- **`references/shield-spec.md`** — Full specification for invariants, session-state, and recovery-protocol files
- **`references/recovery-template.md`** — Exact text to inject into CLAUDE.md for the recovery protocol section
