# Version Check Protocol

Check if the plugin is up to date. Any skill can reference this file.

## Steps

### 1. Get Local Version

```
Read file: ${CLAUDE_PLUGIN_ROOT}/.claude-plugin/plugin.json
```

Extract `"version"`.

### 2. Get Remote Version

```
WebFetch: https://raw.githubusercontent.com/dhanu-nagarajan/bootstrap-claude/main/.claude-plugin/plugin.json
```

Extract `"version"`. If fetch fails (network error, rate limit), skip silently — never block the user's workflow.

### 3. Compare

- Remote newer → show update notice
- Equal → show nothing
- Local newer → show nothing (development build)

### Update Notice

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔄 bootstrap-claude update available: v{local} → v{remote}
   Run /update plugin to upgrade, then /reload-plugins
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Skip Conditions

Do NOT check if:
- User is running `/update plugin` (already updating)
- Skill is invoked as sub-step of another skill
- Already checked this session
