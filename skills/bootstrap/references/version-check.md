# Version Check Protocol

This file defines how to check if the bootstrap-claude plugin is up to date. Any skill can reference this file to perform a version check.

## How to Check

### Step 1: Get Local Version

Read the plugin's local version:

```
Read file: ${CLAUDE_PLUGIN_ROOT}/.claude-plugin/plugin.json
```

Extract the `"version"` field.

### Step 2: Get Remote Version

Fetch the latest version from the GitHub repository:

```
WebFetch: https://raw.githubusercontent.com/dhanu-nagarajan/bootstrap-claude/main/.claude-plugin/plugin.json
```

Extract the `"version"` field from the response.

If the fetch fails (network error, rate limit), skip the version check silently — do not block the user's workflow.

### Step 3: Compare

Compare local version against remote version using semantic versioning:
- If remote is newer: show the update notice (see below)
- If equal: show nothing (user is up to date)
- If local is somehow newer: show nothing (likely a development build)

### Update Notice Format

When the plugin is outdated, display this notice BEFORE the skill's normal output:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔄 bootstrap-claude update available: v{local} → v{remote}
   Run /update plugin to upgrade
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Skip Conditions

Do NOT perform a version check if:
- The user is running `/update plugin` (they're already updating)
- The skill is invoked as a sub-step of another skill (e.g., /doctor run from /update)
- The version was already checked in this session (avoid repeated notices)
