# Lite Tier: Minimum Viable Protection

Generate only the files that deliver immediate, measurable value. This tier proves bootstrap-claude's worth in 30 seconds — compaction protection and forbidden patterns.

---

## Files to Generate

### 1. `.claude/standards/forbidden.md`

Load the standards spec:
```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/specs/standards.md
```

Generate ONLY the `forbidden.md` section. Requirements:
- At least 5 project-specific anti-patterns (derived from linter config, language idioms, observed codebase patterns)
- Each pattern must be verifiable with grep
- Include the language's most dangerous patterns
- Reference actual files where the pattern should NOT appear

### 2. `.claude/shield/invariants.md`

Load the shield spec:
```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/shield/references/shield-spec.md
```

Extract top 10 rules from:
- The generated `forbidden.md`
- Any existing `CLAUDE.md` content
- Verified build/test/lint commands

Must stay under 2000 tokens for quick re-reading after compaction.

### 3. `.claude/shield/session-state.md`

Generate the empty session state template from shield-spec.md.

### 4. `.claude/shield/recovery-protocol.md`

Generate the recovery protocol with project-specific commands.

### 5. CLAUDE.md Update

Load the recovery template:
```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/shield/references/recovery-template.md
```

Inject the recovery protocol section into CLAUDE.md.

## Files NOT Generated (Lite Tier)

- No protocols/ directory
- No standards/ files except forbidden.md
- No templates/ directory
- No checklists/ directory
- No personas/ directory
- No context/ directory

## Post-Generation Output

```
Done! 4 files generated in .claude/ (lite tier)

  Shield:     active (session protected)
  Forbidden:  {N} anti-patterns enforced
  Confidence: {score}%

  Want more? Run /bootstrap --standard for full standards coverage.
  Want everything? Run /bootstrap --full for the complete system.
```

## Validation

Even in lite tier, validate all references:
- File paths in forbidden.md → verify with Glob
- Commands in recovery-protocol.md → verify in package manifest
- Generate `.bootstrap-state.json` with tier: "lite"
