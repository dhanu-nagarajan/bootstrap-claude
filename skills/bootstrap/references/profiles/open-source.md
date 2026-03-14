# Profile: Open Source

Additional standards and rules applied when `.bootstrap-config.yaml` specifies `profile: open-source`.

## Philosophy

Open source projects have unique constraints: contributors have varying skill levels, context is lost between contributors, and documentation IS the product as much as code. Standards must be discoverable, enforceable, and kind.

## Standards Additions

### Architecture
- Public API surface must be explicitly defined and documented
- Breaking changes require deprecation notices one major version before removal
- Internal vs public modules clearly separated (underscore prefix, `internal/` directory, or export maps)
- Plugin/extension points documented with examples

### Language Conventions
- Code must be readable by contributors who don't know the full codebase
- Prefer explicit over clever — no magic, no implicit behavior
- Comments explain WHY, not WHAT — but err on the side of more comments for public APIs
- Examples in docstrings/JSDoc for all public functions

### Testing
- All public API functions have tests that serve as usage documentation
- Test names describe behavior in plain English: `test_returns_empty_list_when_no_items_match`
- CI must pass on all supported platforms before merge
- Include test fixtures that demonstrate common use cases

## Additional Forbidden Patterns

Add to `standards/forbidden.md`:

- Breaking changes without major version bump — Semantic versioning is a contract with users
- Dependencies on pre-release or unpinned packages — Reproducible builds are essential
- Platform-specific code without fallbacks — Unless explicitly documented as platform-specific
- Undocumented public API changes — Every public function/type change needs changelog entry
- Force-pushing to main/master — History preservation required; use revert commits
- Merge commits from personal branches — Squash or rebase to keep history clean
- Binary files in the repository — Use Git LFS or external hosting

## Additional Checklist Items

Add to `checklists/pre-commit.md`:
- [ ] Public API changes documented in CHANGELOG.md
- [ ] New public functions have docstrings/JSDoc with examples
- [ ] No breaking changes on minor/patch versions
- [ ] License headers present on new files (if project uses them)

Add to `checklists/pr-review.md`:
- [ ] CONTRIBUTING.md guidelines followed
- [ ] Commit messages follow project convention
- [ ] Documentation updated for user-facing changes
- [ ] Backward compatibility preserved (or breaking change properly versioned)
- [ ] CI passes on all supported platforms/versions
- [ ] New dependencies justified and license-compatible

Add to `checklists/new-module.md`:
- [ ] Module exported in package entry point (if public)
- [ ] README or doc section added for new feature
- [ ] Example usage added to examples/ directory (if applicable)
- [ ] Migration guide written (if replacing existing functionality)

## Additional Context

Add to `context/domain.md`:
- Supported platforms and versions (Node versions, Python versions, OS, etc.)
- Release process and versioning strategy
- Contribution workflow (fork → branch → PR → review → merge)
- Governance model (BDFL, committee, company-backed, community)
- Key downstream dependents (who breaks if we break?)

## Persona Modifications

### Reviewer Persona
- Add: "Be constructive and educational — contributors may be first-time OSS contributors"
- Add: "Reference CONTRIBUTING.md for style/process questions rather than enforcing personally"
- Add: "Suggest improvements but don't block on style preferences — functionality and correctness first"

### Mentor Persona
- Add: "Link to relevant documentation and examples in the project"
- Add: "Explain project conventions and where they're documented"
- Add: "Guide contributors to the right files and patterns for their contribution"
