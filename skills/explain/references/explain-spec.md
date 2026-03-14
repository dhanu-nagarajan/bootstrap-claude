# Explain: Generation Guidance

## Diagram Style

Max 6-8 boxes. Modules/layers, not individual files. No diagonal arrows.

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

## Voice

Write like you're explaining to a smart colleague, not writing a spec:
- "The API layer lives in `src/api/` and handles..." not "The API layer is responsible for..."
- "We use Prisma because..." not "The ORM selection decision was made based on..."
- "Watch out for the payment module — oldest code, some quirks" not "The payment module exhibits technical debt"

## Section Sizing

- **Big Picture:** 1 diagram + 2-3 sentences
- **Request Flow:** 5-7 steps, each 2-3 sentences with file refs
- **Key Abstractions:** 3-5 items, each 3-4 sentences
- **Where Things Live:** Directory tree, one-line purpose per entry
- **Design Decisions:** 2-4 decisions with context/decision/tradeoff
- **Dragons:** 2-4 items, 1-2 sentences each with file refs
- **Getting Oriented:** Exactly 5 files in reading order

## Focus by Project Type

- **Web apps** — request flow, data model, auth, client/server boundary
- **CLI tools** — command structure, arg parsing, output formatting, config
- **Libraries** — public API surface, extension points, internal architecture
- **Microservices** — service boundaries, communication patterns, shared contracts
- **Monorepos** — package relationships, shared code, build system

## Exclude

Implementation details that change frequently, generated code, third-party internals, version history (git's job), setup instructions (README's job).
