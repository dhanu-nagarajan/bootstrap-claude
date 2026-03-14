# Explain: Generation Guidance

Additional guidance for generating high-quality architecture narratives.

---

## ASCII Diagram Style

Use simple box-and-arrow diagrams. Keep them readable in a terminal.

### Good Example
```
┌──────────┐     ┌──────────┐     ┌──────────┐
│  Client   │────▶│  API     │────▶│  Service │
│  (React)  │◀────│  (Next)  │◀────│  Layer   │
└──────────┘     └──────────┘     └─────┬────┘
                                        │
                                  ┌─────▼────┐
                                  │ Database  │
                                  │ (Prisma)  │
                                  └──────────┘
```

### Bad Example (too complex)
```
Don't create diagrams with more than 6-8 boxes.
Don't use diagonal arrows.
Don't try to show every file — show modules/layers.
```

## Narrative Voice

Write as if explaining to a smart colleague over coffee:
- "The API layer lives in `src/api/` and handles..." (not "The API layer is responsible for...")
- "We use Prisma because..." (not "The ORM selection decision was made based on...")
- "Watch out for the payment module — it's the oldest code and has some quirks" (not "The payment module exhibits technical debt")

## Section Depth

- **Big Picture:** 1 diagram + 2-3 sentences
- **Request Flow:** 5-7 steps, each 2-3 sentences with file references
- **Key Abstractions:** 3-5 abstractions, each 3-4 sentences
- **Where Things Live:** Directory tree with one-line purpose per entry
- **Design Decisions:** 2-4 decisions, each with context/decision/tradeoff
- **Dragons:** 2-4 items, each 1-2 sentences with specific file references
- **Getting Oriented:** Exactly 5 files in suggested reading order

## Handling Different Project Types

### Web Applications (Next.js, Django, Rails)
Focus on: request flow, data model, authentication, client/server boundary

### CLI Tools (Cobra, Click, Clap)
Focus on: command structure, argument parsing, output formatting, configuration

### Libraries/SDKs
Focus on: public API surface, extension points, internal architecture, versioning

### Microservices
Focus on: service boundaries, communication patterns, shared contracts, deployment

### Monorepos
Focus on: package relationships, shared code, build system, dependency management

## What NOT to Include

- Implementation details that change frequently
- Generated code explanations
- Third-party library internals
- Version history (that's git's job)
- Setup instructions (that's README's job)
