# Glossary

Project-specific terms for bootstrap-claude.

| Term | Definition | Reference |
|------|-----------|-----------|
| **Tier** | Generation level controlling how many files are produced: lite (4), standard (14), full (25+). | `skills/bootstrap/references/tiers/` |
| **Shield** | Compaction-resilient session protection system. Three files (invariants, session-state, recovery-protocol) designed to survive context compaction. | `skills/shield/SKILL.md` |
| **Invariants** | Top 10-15 critical rules encoded to survive compaction. Must stay under 2000 tokens. | `.claude/shield/invariants.md` |
| **Registry** | Extensible resource system for examples, profiles, and export formats. Supports project-local overrides. | `registry/registry-spec.md` |
| **Profile** | Industry or organization overlay that adds rules on top of base generation. Never replaces base rules. | `registry/profiles/` |
| **Spec** | Generation specification defining what a category of `.claude/` files contains and how to produce them. | `skills/bootstrap/references/specs/` |
| **Confidence score** | Ratio of verified to total references in generated output. Used to flag unreliable generation. | `core/validation/reference-validator.md` |
| **Fingerprint** | SHA-256 hash of codebase structure used for drift detection between sessions. | `core/fingerprint/fingerprint-spec.md` |
| **Compaction** | Claude Code's context window summarization that can destroy project rules mid-session. The problem shield solves. | `skills/shield/references/shield-spec.md` |
| **Persona** | Interaction mode for generated `.claude/` systems: architect, reviewer, optimizer, debugger, or mentor. | `skills/bootstrap/references/specs/personas.md` |
| **Master prompt** | The orchestrator that coordinates all generation steps. Loads modular specs based on tier selection. | `skills/bootstrap/references/master-prompt.md` |
| **Recovery protocol** | Step-by-step procedure for restoring context after compaction. Must be under 50 lines. | `.claude/shield/recovery-protocol.md` |
| **Drift** | When `.claude/` files no longer match the actual codebase state. Detected by `/doctor`. | `skills/doctor/references/doctor-spec.md` |
