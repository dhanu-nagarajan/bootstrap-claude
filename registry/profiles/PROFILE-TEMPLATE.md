# {Industry/Organization} Profile

Use this template to create a custom industry or organization profile. Profiles add rules ON TOP of base generation — they never replace base rules.

**Instructions:**
1. Copy this file and rename to `{profile-name}.md`
2. Fill in each section with industry-specific requirements
3. Place in `.bootstrap/profiles/` in your project, or submit a PR to add to built-in profiles
4. Reference in `.bootstrap-config.yaml`: `profile: {profile-name}`

---

## Profile: {Name}

### Applies When

```yaml
profile: {name}
```

### Philosophy

{1-2 sentences on what this profile prioritizes and why}

### Additional Forbidden Patterns

{List industry-specific anti-patterns that are NEVER acceptable}

- `{pattern}` — {why it's forbidden in this industry}
- `{pattern}` — {why it's forbidden in this industry}

### Additional Security Requirements

{Compliance frameworks, regulatory requirements, security standards}

#### {Framework Name} (e.g., PCI-DSS, HIPAA, SOC 2)

- {Specific requirement}
- {Specific requirement}

### Modified Standards

{Which base standards get additional rules and what those rules are}

#### Architecture Additions
- {Rule}

#### Testing Additions
- {Rule}

#### Error Handling Additions
- {Rule}

### Additional Checklists

{Industry-specific verification items to add to checklists}

#### Pre-Commit Additions
- [ ] {Check}
- [ ] {Check}

#### PR Review Additions
- [ ] {Check}
- [ ] {Check}

### Persona Adjustments

{How should personas behave differently for this industry}

- **Reviewer:** {adjustment}
- **Architect:** {adjustment}
