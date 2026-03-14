# New Module Checklist

## Adding a New Skill

- [ ] Create `skills/{name}/SKILL.md` with frontmatter (`name` + `description`)
- [ ] Create `skills/{name}/references/` directory for spec files
- [ ] Write spec file(s) in `skills/{name}/references/`
- [ ] Add skill to `CLAUDE.md` directory tree
- [ ] Add skill to `README.md` skills table (update skill count)
- [ ] Add skill to `master-prompt.md` if invoked by bootstrap
- [ ] Add skill to bootstrap `SKILL.md` post-generation guidance
- [ ] Update `plugin.json` description with new skill count
- [ ] Update dependency graph in `CLAUDE.md` if applicable
- [ ] Verify skill loads correctly via `/reload-plugins`

## Adding a Language Example

- [ ] Copy `registry/examples/EXAMPLE-TEMPLATE.md` to `registry/examples/{language}-project.md`
- [ ] Fill in language-specific patterns, conventions, and idioms
- [ ] Add to `master-prompt.md` Step 3 language detection list
- [ ] Add to `CONTRIBUTING.md` supported languages (remove from "needed" list)
- [ ] Add to `README.md` supported languages section
- [ ] Add to `CLAUDE.md` directory tree under `registry/examples/`

## Adding an Export Format

- [ ] Copy `skills/export/references/formats/FORMAT-TEMPLATE.md` to `skills/export/references/formats/{tool}.md`
- [ ] Fill in tool-specific transformation rules and output structure
- [ ] Update `skills/export/SKILL.md` if new trigger keywords needed
- [ ] Add to `CLAUDE.md` directory tree under `skills/export/references/formats/`
- [ ] Add to `README.md` supported export formats

## Adding a Registry Profile

- [ ] Copy `registry/profiles/PROFILE-TEMPLATE.md` to `registry/profiles/{industry}.md`
- [ ] Define industry-specific rules (these ADD to base, never replace)
- [ ] Add to `CLAUDE.md` directory tree under `registry/profiles/`
- [ ] Add to `README.md` available profiles
