# {Language} Project — Reference Output Example

Use this template to create a calibration example for a new language. The example shows what high-quality, project-specific output looks like for this language ecosystem.

**Instructions:**
1. Copy this file and rename to `{language}-project.md`
2. Fill in each section with realistic, specific examples
3. Use a representative framework for the language (e.g., Gin for Go, Spring Boot for Java)
4. Every pattern must be something you'd actually find in a well-maintained project
5. Submit a PR to add it to the registry

---

## Language: {Language Name}
## Framework: {Primary Framework}
## Example Project Type: {API service / CLI tool / web app / library}

## Naming Conventions

### Files
- {Pattern}: `{example-filename}` (e.g., kebab-case: `user-service.ts`)

### Functions/Methods
- {Pattern}: `{exampleFunction}` (e.g., camelCase: `getUserById`)

### Types/Classes
- {Pattern}: `{ExampleType}` (e.g., PascalCase: `UserService`)

### Constants
- {Pattern}: `{EXAMPLE_CONSTANT}` (e.g., SCREAMING_SNAKE: `MAX_RETRY_COUNT`)

## Type Discipline

{Describe the typing approach for this language: strict mode, type inference patterns, boundary validation, generics usage}

Example:
```{language}
// Show a realistic typed function signature
```

## Patterns

### Error Handling
```{language}
// Show the idiomatic error handling pattern for this language
```

### Data Fetching / IO
```{language}
// Show how this ecosystem handles async/IO operations
```

### Module Organization
```
project/
├── {typical directory structure for this language}
```

### Import Organization
```{language}
// Show proper import grouping and ordering
```

## Forbidden Patterns

List 10-15 language-specific anti-patterns:

- `{anti-pattern}` — {why it's bad}
- `{anti-pattern}` — {why it's bad}
- ...

## Testing Conventions

- Test framework: {name}
- Test file naming: `{pattern}` (e.g., `*_test.go`, `test_*.py`)
- Test command: `{command}`
- What to test: {guidance}
- What not to test: {guidance}

## Quality Notes

- This example should demonstrate the MINIMUM level of specificity required
- Every rule should reference realistic file paths and patterns
- No generic advice that applies to all languages
- Focus on what makes THIS language ecosystem unique
