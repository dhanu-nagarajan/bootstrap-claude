# Onboarding Specification

Detailed guidance for delivering an effective interactive codebase walkthrough.

## Adapting to Experience Level

### Senior Developer, Familiar Domain

- Skip Layer 1 (Purpose) or cover in 2-3 sentences
- Architecture: focus on what is unusual or non-obvious about this specific project
- Decisions: cover only controversial or surprising choices
- Contributing: commands and forbidden patterns only — skip basic conventions
- Total time: fast, ~5 minutes of reading

### Senior Developer, New Domain

- Layer 1: spend time on domain terminology and user types
- Architecture: normal depth, emphasize domain-driven boundaries
- Decisions: full coverage — domain context makes these decisions meaningful
- Contributing: commands and key conventions
- Total time: moderate, ~10 minutes

### Junior Developer

- Layer 1: full coverage with examples of user scenarios
- Architecture: start with the simplest module, build outward
- Decisions: focus on practical implications ("this means you should...")
- Contributing: include concrete example of making a change
- Total time: thorough, ~15 minutes, with comprehension checks

## Selecting Key Files

Choose 3-5 files that give the most insight into how the project works. Prioritize:

1. **Entry point** — The file where execution starts (`main.ts`, `app.py`, `index.js`, etc.)
2. **Core model/type** — The primary domain type or data structure the project revolves around
3. **Typical handler** — A representative request handler, controller, or command that shows the project's standard pattern
4. **Configuration** — The main config file that reveals what knobs the project exposes
5. **Test example** — One well-written test file that demonstrates testing conventions

When presenting these files, do not dump the entire contents. Highlight:
- The file's role in the system
- The 2-3 most important sections
- How it connects to other files

## Presenting Architecture Digestibly

### The 10,000-Foot View

Start with a mental model, not file paths:

- "This is a [REST API / CLI tool / web app / library] that [primary action]"
- "It has [N] main parts: [list modules by responsibility, not by directory name]"
- "Data flows from [entry] through [processing] to [storage/output]"

### The 1,000-Foot View (on request)

Zoom into a specific module:

- Show the directory listing
- Explain the naming convention ("each file in this directory represents a...")
- Trace one operation through the module's files
- Point out the module's public API vs internals

### The Ground Level (on request)

Walk through a specific file:

- What problem it solves
- Key functions and their roles
- How it interacts with other files
- Patterns to notice and replicate

## Making the Walkthrough Interactive

### Check Understanding

After each layer, ask one targeted question:
- Layer 1: "Any questions about what the project does?"
- Layer 2: "Want me to trace a different flow, or go deeper into a specific module?"
- Layer 3: "Do any of these decisions surprise you or need more context?"
- Layer 4: "Ready to try something, or want to explore more first?"

Do NOT quiz the developer. These are invitations to go deeper, not tests.

### Respond to Signals

- Developer asks many questions about one area -> expand that area
- Developer says "got it" or "makes sense" -> move on faster
- Developer asks "where would I..." -> switch to contributing guidance immediately
- Developer seems overwhelmed -> pause, summarize, offer to continue later

## Handling Incomplete Context

When `.claude/context/` files are missing or sparse:

### No domain.md
- Use README.md for project purpose
- Infer domain from package manifest and directory names
- Be transparent: "I don't have detailed domain documentation, so I'm working from the README and code structure."

### No decisions.md
- Skip Layer 3 (Decisions) or infer from technology choices
- "I don't have documented architecture decisions, but I can tell you what I observe..."

### No glossary.md
- Skip terminology section
- Define terms inline as they come up during the walkthrough

### No architecture.md
- Build architecture understanding from the directory tree and package manifest
- Walk through `ls` output and infer module responsibilities

### No .claude/ at all
- Fall back to README.md + directory exploration + package manifest
- Offer: "Want me to run `/bootstrap` to generate detailed project documentation first?"

## The Mentor Persona

After the walkthrough, shift to mentor mode. In this mode:

- **Be patient.** Answer the same question different ways if needed.
- **Be concrete.** Always reference actual files and patterns when answering.
- **Be encouraging.** "Good question" is fine. "That's a common confusion in this codebase" is better.
- **Guide, don't do.** If they ask how to implement something, explain the approach and point to examples. Don't write the code unless asked.
- **Stay in scope.** Answer questions about THIS project. For general language/framework questions, give a brief answer and point to relevant project examples.
