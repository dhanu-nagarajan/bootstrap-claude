# Architecture Decision Records

## ADR-001: Tiered Generation (lite/standard/full)

**Status:** Accepted (v3.0.0)

**Context:** v2 generated 25+ files for every project, overwhelming new users and creating unnecessary noise for small projects.

**Decision:** Implement progressive disclosure via three tiers:
- **Lite** (4 files): shield + forbidden — minimal viable protection
- **Standard** (14 files): + standards, checklists, context — default for most projects
- **Full** (25+ files): + protocols, templates, personas — enterprise-grade

**Consequences:** Users adopt incrementally. Tier boundaries must be respected during generation — never produce full-tier files when standard is requested.

**Evidence:** `skills/bootstrap/references/tiers/lite.md`, `skills/bootstrap/references/tiers/standard.md`, `skills/bootstrap/references/tiers/full.md`

---

## ADR-002: Registry System for Extensibility

**Status:** Accepted (v3.0.0)

**Context:** Users need language-specific calibration, industry profiles, and custom export formats without forking the plugin.

**Decision:** Create a registry system with defined resolution order: project-local -> built-in -> community. Provide templates (`EXAMPLE-TEMPLATE.md`, `PROFILE-TEMPLATE.md`, `FORMAT-TEMPLATE.md`) for each extensible category.

**Consequences:** New languages, profiles, and formats can be added by copying a template. The resolution order means project-local overrides always win.

**Evidence:** `registry/registry-spec.md`, `registry/examples/EXAMPLE-TEMPLATE.md`, `registry/profiles/PROFILE-TEMPLATE.md`

---

## ADR-003: Shared Core Infrastructure

**Status:** Accepted (v3.0.0)

**Context:** Analysis, validation, fingerprinting, and output formatting logic was duplicated across multiple skill specs, leading to inconsistency and maintenance burden.

**Decision:** Extract shared logic to `core/` with sub-modules for analysis, validation, fingerprint, and output. All skills load from core rather than maintaining their own copies.

**Consequences:** `core/` changes affect ALL skills — highest impact area. The dependency direction is strictly one-way: `core/` never imports from `skills/` or `registry/`.

**Evidence:** `core/analysis/codebase-analyzer.md`, `core/validation/reference-validator.md`, `core/fingerprint/fingerprint-spec.md`, `core/output/progress-format.md`

---

## ADR-004: Shield as Compaction Protection

**Status:** Accepted (v2.0.0, refined v3.0.0)

**Context:** Claude Code's context window compaction summarizes and discards detailed rules mid-session, causing the AI to forget project constraints and produce lower-quality output.

**Decision:** Redundantly encode critical rules across three files (invariants, session-state, recovery-protocol) designed to survive compaction. Shield is generated during bootstrap (not a separate step) and auto-refreshes at milestones.

**Consequences:** Shield files have strict token budgets (invariants < 2000 tokens, recovery < 50 lines) to ensure they are re-read quickly. The redundancy is intentional, not a design flaw.

**Evidence:** `skills/shield/references/shield-spec.md`, `skills/shield/references/recovery-template.md`, `skills/shield/references/auto-refresh.md`

---

## ADR-005: Plugin Cache Is Not a Git Repo

**Status:** Accepted (v3.0.0, post-incident fix)

**Context:** The plugin self-update mechanism initially used `git pull` to update the plugin cache. This failed because Claude Code's plugin cache is a filesystem snapshot, not a git repository.

**Decision:** Use rsync from a local clone or shallow clone from GitHub for updates. Never execute `git pull` on `${CLAUDE_PLUGIN_ROOT}`.

**Consequences:** The `/update` skill must detect "update plugin" vs "update standards" and use the correct mechanism for each. Version checking uses raw GitHub API, not local git operations.

**Evidence:** `skills/update/SKILL.md` Step 4, commit `aa0e4f7` ("Fix plugin self-update: cache is not a git repo")
