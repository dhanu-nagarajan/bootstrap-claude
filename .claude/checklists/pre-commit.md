# Pre-Commit Checklist

> No automated commands — pure Markdown project. All checks are manual.

## Content Quality

- [ ] No generic advice that could apply to any project in the diff
- [ ] No unverified file path references in specs
- [ ] No `${CLAUDE_PLUGIN_ROOT}` references in generated user-facing output
- [ ] All examples use realistic, project-relevant patterns

## Structural Consistency

- [ ] `CLAUDE.md` directory tree matches actual filesystem
- [ ] All cross-references between files are consistent (e.g., `SKILL.md` -> spec file paths)
- [ ] New files follow established naming conventions

## Impact Assessment

- [ ] If modifying `core/`: verify impact on all skills that load from it
- [ ] If modifying `skills/bootstrap/references/specs/`: verify generated output quality
- [ ] If modifying tier specs: verify file counts match tier definition
- [ ] If adding new files: update `CLAUDE.md` directory tree

## Commit Message

- [ ] Imperative mood, concise (e.g., "Add Go language example", "Fix validation scoring")
- [ ] Message reflects the "why" not just the "what"
