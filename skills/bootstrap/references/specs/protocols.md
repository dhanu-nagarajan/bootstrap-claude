# Spec: Protocols

Generation specification for `.claude/protocols/` — the workflow layer that governs HOW the AI agent operates in a given project.

---

## Files to Generate

| File | Purpose |
|------|---------|
| `core.md` | Base operating doctrine, active during ALL tasks |
| `request.md` | Feature / change request workflow |
| `refresh.md` | Bug fix / root cause analysis workflow |
| `retro.md` | Retrospective / doctrine evolution workflow |

---

## Generation Requirements

### Pre-Generation Analysis

Before writing any protocol file, the generator MUST discover:

1. **Quality gate commands** — Find the project's actual `test`, `lint`, `typecheck`, `build` commands from `package.json` scripts, `Makefile` targets, `Cargo.toml`, CI config, or equivalent. If a command does not exist, do not reference it.
2. **Module boundaries** — Map directory structure to ownership. Identify which directories constitute architectural layers (e.g., `src/api/`, `src/db/`, `src/ui/`).
3. **Risk profile** — Determine what "risky" means for this project: Is it a library consumed by others (API stability matters)? A deployed service (uptime matters)? A CLI tool (backwards compatibility matters)?
4. **Existing workflow conventions** — Check for `CONTRIBUTING.md`, PR templates, CI pipeline definitions. Do not contradict established workflows.

---

## `core.md` — Operational Doctrine

Must include these sections, each grounded in project-specific evidence:

### Core Principles (5-7 max)
- Research-first, trust code over docs, default to action, complete everything, extreme ownership, cascade analysis.
- Adapt language to the project's scale and risk profile. A hobby CLI tool needs different doctrine than a payments service.

### Research-First Protocol
- The 8-step protocol: Discovery (read docs, map system, inspect existing) then Verification (verify understanding, check blockers) then Execution (proceed autonomously, update docs).
- Specify WHEN to apply full protocol vs. when to skip (based on change scope relative to THIS project's complexity).

### Quality Standards
- **MUST reference the project's actual commands.** Example: "Run `npm run test` before committing" — not "run the test suite."
- Include every quality gate that exists: typecheck, lint, test, build. Omit any that do not exist.
- Define what "done" means for this specific project.

### Cascade Analysis
- Reference the project's actual module boundaries.
- Specify: "When modifying `src/X/`, check consumers in `src/Y/` and `src/Z/`" — using real directory names.
- Identify the project's dependency graph for ripple-effect checking.

### Autonomous Execution Rules
- Define proceed-vs-ask thresholds calibrated to this project's risk profile.
- A low-risk internal tool: proceed more aggressively. A public API: stop and ask on any breaking change.

### Communication Style
- No sycophancy, lead with conclusions, structured data over prose, brief acknowledgments only.

---

## `request.md` — Feature / Change Protocol

A phased workflow. **Adapt the number of phases to the project's complexity** — do not use a fixed count. A simple project may need 3 phases; a complex one may need 6+.

### Required Phases (adapt as needed)

1. **Reconnaissance** — Read-only. Understand the relevant code, find existing patterns, identify the impact surface. Reference THIS project's architecture layers by name.
2. **Planning** — Restate objectives, map impact surface using the project's actual module structure, justify implementation strategy.
3. **Execution** — Read-write-reread cycle. Maintain workspace purity. Take system-wide ownership of changes.
4. **Verification** — Run the project's actual quality gate commands. Apply autonomous correction for failures.
5. **Self-Audit** — Re-verify all changes. Hunt for regressions across module boundaries identified in cascade analysis.
6. **Final Report** — Summarize changes made, evidence of correctness, and final verdict.

### Project-Specific Adaptations
- Reference the project's actual architecture layers when describing reconnaissance scope.
- Use the project's actual test/lint/build commands in the verification phase.
- If the project has a CI pipeline, reference it as the final verification gate.

---

## `refresh.md` — Bug Fix / RCA Protocol

A phased workflow for debugging and fixes. **Adapt the number of phases to the project's complexity.**

### Required Phases (adapt as needed)

1. **Reconnaissance** — Read-only baseline. Understand the expected behavior from tests, docs, or code contracts.
2. **Isolate** — Define correctness criteria. Create a failing test if possible. Pinpoint the trigger condition.
3. **Root Cause Analysis** — Hypothesis-experiment-conclude loop. Each iteration must be documented.
4. **Remediation** — Minimal, precise fix targeting the confirmed root cause.
5. **Verification** — Confirm the fix resolves the issue. Run ALL quality gate commands (not just the relevant test).
6. **Self-Audit** — Check for regressions. Verify the fix does not break adjacent modules.
7. **Final Report** — Root cause, fix applied, evidence of correctness.

### FORBIDDEN Actions
- No fix without a confirmed root cause.
- No re-trying a failed approach without new information.
- No symptom-patching (hiding the error instead of fixing the cause).

### Project-Specific Adaptations
- Reference the project's actual test commands for creating failing tests.
- Reference the project's logging/debugging tools for the RCA phase.
- Reference known fragile areas of the codebase if discoverable from git history.

---

## `retro.md` — Retrospective Protocol

A 4-phase workflow for evolving the engineering system itself.

1. **Session Analysis** — Catalog successes, failures, and mid-course corrections from the session.
2. **Lesson Distillation** — Filter for lessons that are universal (not one-off), abstracted (not overly specific), and high-impact (worth codifying).
3. **Doctrine Integration** — Refine existing `.claude/` rules. Modify in place; do not just append. Remove rules that proved unhelpful.
4. **Final Report** — Diff of doctrine changes. Session learnings summary.

---

## Validation Checklist

After generating protocol files, verify:

- [ ] Every shell command referenced in `core.md` exists in the project's package manifest, Makefile, or equivalent (verify via Grep)
- [ ] Every directory path referenced in cascade analysis exists (verify via `ls`)
- [ ] The risk profile description matches the project's actual deployment model (library vs. service vs. CLI vs. other)
- [ ] No fixed phase counts ("6-phase", "7-phase") — phases are adapted to project complexity
- [ ] No generic placeholder commands — every command is the project's real command
- [ ] `request.md` and `refresh.md` reference the project's actual architecture layers by name
- [ ] Would a principal engineer find each protocol actionable for THIS specific project, or delete it as generic noise?
