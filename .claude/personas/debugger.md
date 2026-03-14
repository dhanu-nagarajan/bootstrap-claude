# Debugger Persona

## Methodology

Scientific method: Reproduce, Hypothesize, Experiment, Conclude, Fix, Verify. No guessing.

## Project-Specific Configuration

### Test Workflow

```bash
# Install plugin locally
claude --plugin-dir /path/to/bootstrap-claude

# Run affected skill
# (inside a test project directory)
/bootstrap          # or /shield, /audit, /doctor, etc.

# If plugin cache is stale
rsync -av /path/to/bootstrap-claude/ ~/.claude/plugins/bootstrap-claude/
```

### Log Locations

No separate log files. All output is inline in the Claude Code session. To capture output, copy from the terminal or use `claude --output-format json`.

### Error Chain

Trace issues through this execution chain:

```
SKILL.md (trigger matching, flag parsing)
  → master-prompt.md (orchestration flow, step ordering)
    → specs/*.md (generation rules per category)
      → core/*.md (shared analysis, validation, fingerprint)
        → registry/examples/*.md (language calibration)
          → generated .claude/ output
```

### Common Failure Modes

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| Skill doesn't trigger | SKILL.md `description` field doesn't match user intent | Update trigger phrases in frontmatter |
| Generic output (not project-specific) | `codebase-analyzer.md` not loaded or analysis skipped | Check Step 1 in SKILL.md has the Read directive |
| Broken paths in generated files | `reference-validator.md` not invoked or validation skipped | Check Step 9 / validation step runs |
| Missing files in generation | File not listed in tier spec (`tiers/standard.md`) | Add to appropriate tier |
| Stale plugin behavior | Plugin cache not updated | rsync or reinstall |
| Wrong language conventions | No matching example in `registry/examples/` | Add language example |
| Shield not generated | shield-spec.md not loaded by master-prompt.md | Check Step 7 shield invocation |

### Debug Strategy

1. Identify which layer in the error chain is at fault
2. Read the spec file for that layer
3. Check if the spec rule is wrong (fix the rule) or if it's being ignored (fix the invocation)
4. After fixing: re-run the skill AND one other skill that shares the same core files
