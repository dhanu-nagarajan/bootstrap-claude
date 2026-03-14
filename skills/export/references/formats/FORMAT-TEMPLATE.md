# {Tool Name} Export Format

Use this template to add export support for a new AI coding tool.

**Instructions:**
1. Copy this file and rename to `{tool-name}.md`
2. Fill in each section based on the tool's documentation
3. Place in `.bootstrap/formats/` in your project, or submit a PR to add to built-in formats
4. Use with: `/export {tool-name}` or add to `export.custom_formats` in config

---

## Tool: {Tool Name}

### Output Files

{What files to create and where}

- **Primary file:** `{path/to/file}` (e.g., `.tool/rules.md`)
- **Additional files:** {if the tool supports multiple rule files}

### Format Requirements

{Describe the exact format the tool expects}

- **Frontmatter:** {YAML/TOML/none, required fields}
- **Structure:** {section headers, list format, code blocks}
- **Max length:** {token/line budget per file}
- **Tone:** {directive/conversational/technical}

### Section Layout

```
{Show the exact structure of the output file}
```

### Capability Matrix

| Capability | Supported |
|---|---|
| Agentic workflow | Yes / No / Partial |
| File-scoped rules | Yes / No |
| Multiple rule files | Yes / No |
| Glob matching | Yes / No |
| Code examples | Yes / No |
| Context budget | Small / Medium / Large |

### Transformation Rules

{How to adapt .claude/ content for this tool}

1. **Architecture rules →** {how to present}
2. **Naming conventions →** {how to present}
3. **Forbidden patterns →** {how to present}
4. **Testing rules →** {how to present}
5. **Security rules →** {how to present}

### What to Include

{Priority order for this tool's context budget}

1. {Highest priority category}
2. {Second priority}
3. ...

### What to Exclude

{What doesn't work well with this tool}

- {Category or content type to skip and why}

### Conflict Handling

{How to handle existing files}

- If tool-native rules already exist: {merge/skip/backup}
- Mark generated content with: `{generation marker}`
- Preserve user-written sections: {yes/no, how}
