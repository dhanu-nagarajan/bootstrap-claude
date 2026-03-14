# Recovery Template for CLAUDE.md

This file contains the exact text to inject into the project's CLAUDE.md file. Insert this section near the top of CLAUDE.md — after any project overview section, but before detailed configuration sections. The goal is maximum visibility: this section must survive context compaction summarization itself.

---

## Injection Text

Insert the following markdown block into CLAUDE.md:

```markdown
## CRITICAL: Post-Compaction Recovery Protocol

**This section MUST be read after ANY context compaction event.**

After context compaction, your previous instructions may have been summarized and critical details lost. Before doing ANY work:

1. **STOP** all in-progress operations immediately
2. **READ** `.claude/shield/invariants.md` — Your binding constraints for this project
3. **READ** `.claude/shield/session-state.md` — Your current task and progress
4. **READ** `.claude/shield/recovery-protocol.md` — Full recovery procedure
5. **VERIFY** by stating back: (a) current task, (b) approach, (c) top 3 constraints
6. **RESUME** from the last completed step documented in session-state.md

DO NOT skip these steps. DO NOT assume you remember correctly. Re-read the files.

### Session State Maintenance

During active work, update `.claude/shield/session-state.md` at every significant milestone:
- When completing a major step
- When discovering something important
- When an approach fails
- When switching to a different file or module
```

---

## Placement Rules

1. **Position:** Place immediately after the project overview / first section of CLAUDE.md. It must appear before any detailed standards, protocols, or routing tables — those are the sections most likely to get compacted.

2. **If CLAUDE.md already has a recovery section:** Replace the existing section entirely with the text above. Do not create duplicates.

3. **If CLAUDE.md is empty or minimal:** Add a brief `## Project Overview` header with the project name first, then add the recovery section.

4. **Do not modify any other sections** of CLAUDE.md during this step. The recovery template injection is a surgical addition.

## Why This Section Must Be in CLAUDE.md

CLAUDE.md is the one file that Claude Code is most likely to retain awareness of after compaction. It is the root configuration file. By placing the recovery protocol here — not just in `.claude/shield/` — we create a redundant trigger: even if Claude forgets about the shield directory entirely, the CLAUDE.md reminder will prompt it to re-read the shield files.
