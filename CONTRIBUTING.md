# Contributing to bootstrap-claude

Thanks for your interest in contributing! bootstrap-claude is an open-source Claude Code plugin that helps developers generate validated project rules.

## Quick Start

1. Fork and clone the repository
2. Make your changes
3. Test by installing locally: `claude --plugin-dir /path/to/bootstrap-claude`
4. Run `/bootstrap` on a test project to verify your changes
5. Submit a PR

## What You Can Contribute

### Easy (Great First Contributions)

**Add a language example** — We need calibration examples for more languages. Copy an existing example and adapt it:

```
registry/examples/EXAMPLE-TEMPLATE.md  ← Start here
registry/examples/typescript-project.md ← Reference quality
```

Languages needed: C#, Swift, Kotlin, PHP, Ruby, Scala

**Add an export format** — Support a new AI tool. Copy the template:

```
skills/export/references/formats/FORMAT-TEMPLATE.md  ← Start here
skills/export/references/formats/cursor.md           ← Reference
```

Tools needed: Windsurf, Zed, Continue.dev, Tabnine

### Medium

**Add an industry profile** — Create standards for a new industry:

```
registry/profiles/PROFILE-TEMPLATE.md  ← Start here
registry/profiles/fintech.md           ← Reference
```

Profiles needed: Gaming, GovTech, E-commerce, EdTech, ML/AI

**Improve existing specs** — Make generation specs produce better output. Test thoroughly.

### Advanced

**New skills** — Propose new skills via GitHub Issues first to discuss design.

**Core infrastructure** — Changes to `core/` affect all skills. Requires careful testing.

## File Impact Hierarchy

Changes have different blast radii:

1. **`core/`** — Affects ALL skills. Test everything.
2. **`skills/bootstrap/references/specs/`** — Affects generated output quality. Test with multiple project types.
3. **`skills/bootstrap/references/master-prompt.md`** — Controls generation flow. Test full bootstrap cycle.
4. **Individual `SKILL.md` files** — Affects one skill. Test that skill.
5. **`registry/examples/`** — Affects one language. Test with a project in that language.
6. **`registry/profiles/`** — Affects one industry. Test with appropriate project.
7. **`skills/export/references/formats/`** — Affects one export target. Test that export.

## Quality Standards

All content must meet the quality bar:

> Would a principal engineer find this useful, or delete it as noise?

Specifically:
- **No generic advice.** Every rule must be specific and actionable.
- **Evidence-based.** Reference actual code patterns, not hypotheticals.
- **Validated.** File paths, commands, and symbols must be verifiable.
- **Minimal.** No filler, no padding, no "best practices everyone knows."

## Testing Your Changes

Since this is an all-Markdown plugin, testing means running the affected skills on real codebases:

1. **Find or create a test project** in the relevant language/framework
2. **Run the affected skill** (`/bootstrap`, `/audit`, `/export`, etc.)
3. **Verify the output** — Are references valid? Is content specific? Does it meet the quality bar?
4. **Test edge cases** — Empty projects, monorepos, projects without tests, etc.

Include a description of your testing in the PR.

## Commit Style

Imperative mood, concise:
- "Add Go language example"
- "Fix validation for Rust symbol detection"
- "Update export format for Cursor v2 rules"

## Pull Request Process

1. Create a descriptive PR title
2. Describe what changed and why
3. Describe how you tested
4. Link to any related issues

## Code of Conduct

Be respectful, constructive, and helpful. We're all here to make AI-assisted development better.
