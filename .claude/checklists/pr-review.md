# PR Review Checklist

## 1. Correctness

- [ ] Spec instructions produce the intended output
- [ ] Generation rules are clear and unambiguous
- [ ] No logical contradictions between specs
- [ ] Tier file lists are accurate

## 2. Specificity

- [ ] Every rule is grounded in actual project evidence
- [ ] No generic advice that could apply to any codebase
- [ ] Examples reference real patterns from the target domain

## 3. Architecture

- [ ] Follows the layer model: `core/` -> `skills/` -> `registry/`
- [ ] Respects module boundaries (`core/` never imports from `skills/` or `registry/`)
- [ ] Skills are self-contained (`SKILL.md` + `references/`)
- [ ] Registry resources are pure data (no imports)

## 4. Consistency

- [ ] Cross-references between files match (paths, names, counts)
- [ ] Naming conventions followed throughout
- [ ] `CLAUDE.md` directory tree matches actual filesystem
- [ ] Skill count in `plugin.json` description matches reality

## 5. Token Budgets

- [ ] Shield `invariants.md` under 2000 tokens
- [ ] Shield `recovery-protocol.md` under 50 lines
- [ ] Specs are appropriately sized (not bloated with filler)

## 6. Forbidden Patterns

- [ ] No `${CLAUDE_PLUGIN_ROOT}` in user-facing output
- [ ] No `git pull` on plugin cache
- [ ] No removal of forbidden/security rules
- [ ] No blocking on version check failures
- [ ] No unverified references

## 7. Extensibility

- [ ] New specs follow existing templates
- [ ] New profiles follow `PROFILE-TEMPLATE.md`
- [ ] New formats follow `FORMAT-TEMPLATE.md`
- [ ] New examples follow `EXAMPLE-TEMPLATE.md`
- [ ] Registry resolution order preserved (project-local -> built-in -> community)
