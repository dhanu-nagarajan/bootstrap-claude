# Testing Standards

No traditional test suite exists. All files are Markdown/JSON/YAML with no runtime. Validation serves as the test layer.

## Manual Testing Workflow

Install the plugin locally and run skills against real projects:

```bash
claude --plugin-dir /path/to/bootstrap-claude
```

Then invoke skills (`/bootstrap`, `/shield`, `/audit`, etc.) on test projects of different languages and frameworks. Verify:
1. Generated files are project-specific, not generic
2. All file paths, commands, and symbols in generated output actually exist in the target project
3. Token budgets are respected (invariants.md < 2000 tokens, recovery-protocol.md < 50 lines)

## Validation Is the Test

`core/validation/reference-validator.md` defines three verification types that serve as automated correctness checks:

| Validation Type | What It Checks | Failure Threshold |
|---|---|---|
| File path validation | Every backticked path exists via Glob | Exact or fuzzy match required |
| Command validation | CLI commands exist in package.json scripts, Makefile targets, or language built-ins | Must trace to manifest |
| Code symbol validation | PascalCase/camelCase/snake_case references exist in source via Grep | Declaration context preferred |

Confidence scoring per generated file: `verified_count / total_references * 100`. Files below 70% must be re-analyzed and regenerated. Files between 70-89% require review of unverified items. See `core/validation/reference-validator.md` lines 67-76.

## Cross-Reference Consistency

The directory tree in `CLAUDE.md` must match the actual filesystem. When adding, renaming, or removing files:

1. Verify the `CLAUDE.md` "Repository Structure" section reflects the change
2. Verify any `Read file:` directives pointing to moved/renamed files are updated across all specs
3. Run `find` on the actual directory structure and compare against documented trees

Key files that reference the directory structure:
- `CLAUDE.md` — full repository tree
- `skills/bootstrap/references/master-prompt.md` — references to tier specs, category specs, registry resources
- `skills/shield/SKILL.md` — references to shield spec files

## Showcase as Golden Output

`showcase/` contains example generated output for evaluation:

| Showcase | Project Type | Tier |
|---|---|---|
| `showcase/nextjs-app/` | Next.js application | standard |
| `showcase/fastapi-service/` | FastAPI service | standard |
| `showcase/rust-cli/` | Rust CLI tool | lite |

When modifying generation specs (`skills/bootstrap/references/specs/*.md`), compare new output against these showcases to detect quality regressions. The showcase output sets the specificity floor.

## When Adding New Specs

Before merging a new spec file (in `skills/bootstrap/references/specs/`, `registry/examples/`, `registry/profiles/`, or `skills/export/references/formats/`):

1. Run the relevant skill on at least one real project matching the spec's target
2. Verify generated output passes reference validation at >= 90% confidence
3. Confirm the spec follows the template structure (see `EXAMPLE-TEMPLATE.md`, `PROFILE-TEMPLATE.md`, or `FORMAT-TEMPLATE.md`)
4. Verify the new file is referenced in the appropriate loading logic (`master-prompt.md` for examples, `registry-spec.md` for registry resources)

## When Modifying Core Infrastructure

Changes to `core/` affect all 9 skills. Before merging changes to:
- `core/analysis/codebase-analyzer.md` — test with projects in at least 2 different languages
- `core/validation/reference-validator.md` — verify confidence scoring still produces sensible thresholds
- `core/output/progress-format.md` — verify output renders correctly in terminal
