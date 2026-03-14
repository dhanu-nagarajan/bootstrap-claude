# Bug Fix Workflow

Four phases. No fix without root cause understanding.

## Phase 1: Reproduce

- Install the plugin: `claude --plugin-dir /path/to/bootstrap-claude`
- Run the affected skill on a test project
- Observe and document the exact problem (wrong output, missing file, broken reference, crash)
- Note the skill invocation and any flags used

## Phase 2: Root Cause

Trace through the execution chain:

```
SKILL.md (trigger + flags)
  → master-prompt.md (orchestration, if /bootstrap)
    → spec file (generation rules)
      → core/ file (shared infrastructure)
        → generated output
```

- Identify which layer produces the fault
- Form a hypothesis before making changes
- Check if the issue is in the spec (wrong rule) or the core (wrong shared behavior)

## Phase 3: Fix

- Patch the minimal set of files — prefer surgical edits over rewrites
- If paths changed: update all cross-references (CLAUDE.md tree, README, other specs)
- If a core file changed: verify all consumers still work (see cascade table in `core.md`)

## Phase 4: Verify

- Re-run the skill that was broken — confirm the fix
- Run at least one other skill that shares the same `core/` files — confirm no regression
- If the fix changed `master-prompt.md`: test /bootstrap on a fresh project

## Forbidden

- No fix without understanding root cause
- No "try and see" without a hypothesis
- No changing `core/` files without checking all consumers
- No path changes without grepping for stale references
