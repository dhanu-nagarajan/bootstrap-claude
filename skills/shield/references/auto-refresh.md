# Shield: Auto-Refresh Protocol

Automatically update session-state.md at key milestones during a Claude Code session, without requiring manual `/shield` invocations.

---

## When to Auto-Refresh

Session state should be updated at these milestone events:

### 1. Major Step Completion
When a significant task step is completed (file created, test passing, feature implemented):
- Update "Completed Steps" with the new item
- Update "Working Files" if new files were touched

### 2. Failed Approach
When an approach is attempted and fails:
- Add to "Failed Approaches" with reason
- This is CRITICAL — prevents post-compaction retry of failed approaches

### 3. Key Discovery
When something important is learned about the codebase:
- Add to "Key Discoveries"
- Include file reference where it was found

### 4. Module Switch
When moving from one module/area to another:
- Update "Working Files" table
- Note the transition in "Completed Steps"

### 5. Task Change
When the user changes tasks or adds a new objective:
- Update "Current Task" and "Acceptance Criteria"
- Archive previous task state under "Completed Steps"

## How to Auto-Refresh

### Reading Current State
```
Read file: .claude/shield/session-state.md
```

### Updating
Use the Edit tool to surgically update only the changed sections. Never rewrite the entire file.

### Update Format

Each update should:
1. Update `**Last updated:**` timestamp
2. Add/modify only the relevant section
3. Keep entries concise (one line per item)
4. Use checkboxes for completed steps: `- [x] Step description`

## Configuration

Auto-refresh is controlled by `.bootstrap-config.yaml`:

```yaml
shield:
  auto_refresh: true  # default: true
```

When `auto_refresh: false`, session-state.md is only updated via manual `/shield` invocations.

## Integration with Recovery

After context compaction, the recovery protocol reads session-state.md. The more current this file is, the better the recovery:

- **Stale session-state** → Recovery resumes from an old checkpoint, repeating work
- **Current session-state** → Recovery resumes from the last milestone, minimal rework

## Lightweight Updates

Auto-refresh should be lightweight — don't interrupt flow:
- No user confirmation needed
- No progress output (silent update)
- Takes <5 seconds
- Only updates changed sections
