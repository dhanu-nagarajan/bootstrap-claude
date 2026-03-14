# New Skill Template

The most common repeating pattern in this plugin. 9 existing skills follow this structure.

## Files to Create

```
skills/{{name}}/
  SKILL.md                              # Entry point (triggers, flags, execution steps)
  references/
    {{name}}-spec.md                    # Detailed generation/analysis rules (if needed)
```

## SKILL.md Skeleton

```markdown
---
name: {{name}}
description: >-
  This skill should be used when the user asks to "{{trigger phrases}}"
  or invokes /{{name}}. {{What it does in one sentence}}.
---

# {{Name}}: {{Title}}

{{One-line description of what it generates or does.}}

**Quality bar:** {{Measurable quality requirement specific to this skill.}}

## Execution

### Step 1: {{Analysis/Input}}

` ` `
Read file: ${CLAUDE_PLUGIN_ROOT}/core/analysis/codebase-analyzer.md
` ` `

{{What to analyze and why.}}

### Step 2: {{Generation/Action}}

` ` `
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/{{name}}/references/{{name}}-spec.md
` ` `

{{What to produce. Be specific about output format and location.}}

### Step 3: {{Validation}}

` ` `
Read file: ${CLAUDE_PLUGIN_ROOT}/core/validation/reference-validator.md
` ` `

{{Verify all generated paths, commands, and references exist.}}

## Quality Principles

1. {{Principle grounded in this skill's purpose}}
2. {{Principle that prevents common failure mode}}

## Additional Resources

- **`references/{{name}}-spec.md`** — {{What rules it contains}}
```

## Post-Scaffolding Checklist

After creating the skill files, update these cross-references:

1. **CLAUDE.md** — add to directory tree under `skills/`
2. **CLAUDE.md** — add to skill dependency graph
3. **CLAUDE.md** — update layer architecture diagram if needed
4. **README.md** — update skill count and add to skills table
5. **`skills/bootstrap/SKILL.md`** — update post-generation guidance if the new skill interacts with /bootstrap
6. **`.claude-plugin/plugin.json`** — update description with new skill count
7. **Test** — run via `/reload-plugins` then invoke the skill on a test project

## Reference: Existing Skills to Study

| Skill | Complexity | Good Example Of |
|-------|-----------|-----------------|
| `/shield` | Simple | Standalone skill with auto-refresh |
| `/audit` | Medium | CI mode, badge generation, scoring |
| `/bootstrap` | Complex | Multi-step orchestration, tier-aware |
| `/roast` | Medium | Analysis + scoring rubric |
| `/explain` | Medium | Narrative generation, diagram output |
