# Full Tier: Complete Engineering System

The maximum generation tier. Includes everything from standard tier plus protocols, templates, personas, and additional standards/checklists/context files. This is the v2 default, now opt-in.

---

## Files to Generate

### From Standard Tier (always included)

Files 1-14 from standard tier.

### Standards (up to 3 additional files)

Continue from the standards spec. Generate:

15. `.claude/standards/error-handling.md` — Error classification, boundaries, logging, recovery
16. `.claude/standards/performance.md` — Budget, lazy loading, memoization, measurement
17. `.claude/standards/components.md` — Component classification, composition, state, styling, a11y (**only if UI layer exists**)

### Protocols (4 files)

Load the protocols spec:
```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/specs/protocols.md
```

Generate:
18. `.claude/protocols/core.md` — Base doctrine, research protocol, quality standards
19. `.claude/protocols/request.md` — Feature/change workflow
20. `.claude/protocols/refresh.md` — Bug fix/RCA workflow
21. `.claude/protocols/retro.md` — Doctrine evolution

### Templates (2-4 files)

Load the templates spec:
```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/specs/templates.md
```

Generate 2-4 project-specific template files based on repeating patterns found in the codebase.

### Personas (5 files)

Load the personas spec:
```
Read file: ${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/specs/personas.md
```

Generate:
22. `.claude/personas/architect.md` — System design, ADRs, trade-offs
23. `.claude/personas/reviewer.md` — Code review
24. `.claude/personas/optimizer.md` — Performance analysis
25. `.claude/personas/debugger.md` — Debugging methodology
26. `.claude/personas/mentor.md` — Teaching and explanation

### Checklists (2 additional files)

Continue from checklists spec:
27. `.claude/checklists/new-module.md` — New module creation workflow
28. `.claude/checklists/incident.md` — Incident response procedure

### Context (1 additional file)

Continue from context spec:
29. `.claude/context/domain.md` — Problem statement, core concepts, domain algorithms

## Post-Generation Output

```
Done! {N} files generated in .claude/ (full tier)

  Shield:     active (session protected)
  Standards:  {N} rules across {N} files
  Protocols:  4 phased workflows
  Personas:   5 interaction modes
  Templates:  {N} scaffolding patterns
  Checklists: 4 verification gates
  Context:    domain knowledge, {N} ADRs, {N} glossary terms
  Confidence: {score}%

  This is the complete engineering system.
```

## When to Recommend Full Tier

- Teams with 3+ developers using Claude Code
- Long-running projects with established conventions
- Projects that need phased workflows and interaction modes
- Users who have already used standard tier and want more
