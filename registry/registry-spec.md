# Registry: Extensible Spec/Profile/Format/Example System

The registry enables extensibility without forking. Users and communities can add custom generation specs, industry profiles, language examples, and export formats.

---

## Resolution Order

When loading a resource, the registry checks three sources in priority order:

1. **Project-local** — `.bootstrap/specs/`, `.bootstrap/profiles/`, `.bootstrap/formats/`, `.bootstrap/examples/`
2. **Built-in** — `${CLAUDE_PLUGIN_ROOT}/registry/` (ships with the plugin)
3. **Community** — fetched from GitHub on demand, cached locally (future feature)

The first match wins. This allows project-local overrides of built-in resources.

## Resource Types

### Specs (Generation Categories)

Specs define what to generate in a category. Each spec produces one or more `.claude/` files.

**Built-in specs:** protocols, standards, templates, checklists, personas, context

**Location:** `${CLAUDE_PLUGIN_ROOT}/skills/bootstrap/references/specs/` (built-in), `.bootstrap/specs/` (custom)

**Loading:** Based on tier configuration:
- **lite:** standards (forbidden.md only), shield
- **standard:** standards, checklists, context, shield
- **full:** all specs (protocols, standards, templates, checklists, personas, context, shield)

Additional specs from `specs.include` config are loaded regardless of tier.
Specs listed in `specs.exclude` are skipped regardless of tier.

### Profiles (Industry Overlays)

Profiles add industry-specific rules on top of base generation. They never replace base rules.

**Built-in profiles:** fintech, healthcare, startup, open-source

**Location:** `${CLAUDE_PLUGIN_ROOT}/registry/profiles/` (built-in), `.bootstrap/profiles/` (custom)

**Loading:** From `profile` field in `.bootstrap-config.yaml`. Can be a built-in name or a file path.

### Examples (Language Calibration)

Examples demonstrate the required specificity level for each language. They calibrate generation quality — output must be THIS specific, never more generic.

**Built-in examples:** typescript, python, rust, go, java

**Location:** `${CLAUDE_PLUGIN_ROOT}/registry/examples/` (built-in)

**Loading:** Auto-detected from project language. Multiple examples can be loaded for multi-language projects.

### Export Formats

Format specs define how to transform `.claude/` content into each AI tool's configuration format.

**Built-in formats:** cursor, copilot, aider, cline, agents

**Location:** `${CLAUDE_PLUGIN_ROOT}/skills/export/references/formats/` (built-in), `.bootstrap/formats/` (custom)

**Loading:** From `/export <target>` command or `export.targets` config.

## Custom Resource Structure

### Custom Spec

```markdown
# {Category Name} Spec

## What This Generates
{List of files this spec creates in .claude/}

## Analysis Requirements
{What to look for in the codebase}

## Generation Rules
{How to produce the output}

## Output Format
{Exact structure of each generated file}

## Validation Requirements
{What references to verify}
```

### Custom Profile

```markdown
# {Industry/Org} Profile

## Applies When
profile: {name}

## Additional Forbidden Patterns
{Industry-specific anti-patterns}

## Additional Security Requirements
{Compliance frameworks, regulatory needs}

## Modified Standards
{Which base standards to augment and how}

## Additional Checklists
{Industry-specific verification items}
```

### Custom Export Format

```markdown
# {Tool Name} Export Format

## Output Files
{What files to create, where}

## Structure
{Section layout, frontmatter, format requirements}

## Token Budget
{Max lines/tokens per file}

## Transformation Rules
{How to adapt .claude/ content for this tool}

## Capability Matrix
{What this tool supports: agentic, file-scoped, glob matching, etc.}
```

## Configuration

```yaml
# .bootstrap-config.yaml
version: 3

tier: standard

profile: fintech
# OR: profile: ./.bootstrap/profiles/my-company.md

specs:
  include:
    - ./.bootstrap/specs/graphql.md      # local custom spec
    - ./.bootstrap/specs/accessibility.md # local custom spec
  exclude:
    - personas                            # skip persona generation

export:
  targets: [cursor, copilot]
  custom_formats:
    - ./.bootstrap/formats/windsurf.md   # local custom format
```

## Future: Community Registry

A planned GitHub-based registry will allow:
```yaml
specs:
  include:
    - community:graphql          # fetch from community registry
    - community:accessibility    # fetch from community registry
profiles:
  - community:gaming            # community-contributed profile
```

Community resources will be cached locally after first fetch and updated on `/update`.
