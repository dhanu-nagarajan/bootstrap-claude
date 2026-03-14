# Changelog

All notable changes to bootstrap-claude are documented here.

## [3.0.0] - 2026-03-14

### Added
- **Tiered generation:** `--lite` (3 files), `--standard` (12 files, default), `--full` (25+ files)
- **`/roast` skill:** Shareable codebase quality report with scores
- **`/explain` skill:** Architecture narrative generator
- **Core infrastructure:** Shared analysis, validation, fingerprinting, and output protocols
- **Registry system:** Extensible specs, profiles, examples, and export formats
- **Config schema:** JSON Schema validation for `.bootstrap-config.yaml`
- **`--dry-run` flag:** Preview output without writing files
- **`--verbose` flag:** Show analysis reasoning
- **`--json` flag:** Machine-readable output for CI
- **CI audit mode:** Exit codes and JSON output for GitHub Actions
- **Compliance badge:** Generate README badge after `/audit`
- **5 new language examples:** Go, Java, C#, Swift, Kotlin
- **Showcase directory:** Real generated output for evaluation
- **CONTRIBUTING.md:** Contributor guide with templates
- **FORMAT-TEMPLATE.md:** Guide for adding custom export formats
- **EXAMPLE-TEMPLATE.md:** Guide for adding language examples
- **PROFILE-TEMPLATE.md:** Guide for adding industry profiles
- **Golden file tests:** Fixture codebases with expected output

### Changed
- Default tier changed from full to standard (12 files)
- Examples and profiles moved to `registry/` for extensibility
- Master prompt updated for tier-aware generation
- Specs load from registry instead of hardcoded paths
- Progress output standardized across all skills
- Plugin version bumped to 3.0.0

### Improved
- Shield auto-refresh at milestones (configurable)
- Audit supports CI mode with exit codes
- Doctor report includes per-file confidence scores
- Export supports custom format specs

## [2.0.0] - 2025-12-01

### Added
- 7-skill enterprise engineering system
- Shield compaction-resilient session protection
- Audit standards compliance checking
- Doctor drift detection
- Update incremental maintenance + plugin self-update
- Export to Cursor, Copilot, Aider, Cline, AGENTS.md
- Onboard interactive codebase walkthrough
- Organization profiles (fintech, healthcare, startup, open-source)
- Language examples (TypeScript, Python, Rust)
- Reference validation with confidence scoring
- Version checking and self-update

## [1.0.0] - 2025-10-01

### Added
- Initial release
- Bootstrap skill for .claude/ generation
