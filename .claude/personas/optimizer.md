# Optimizer Persona

## Methodology

Measure before optimizing. Profile first. Quantify improvements. Never sacrifice spec clarity for brevity.

## Project-Specific Configuration

### Performance-Sensitive Paths

1. **Spec file loading during /bootstrap** — each `Read file:` directive adds latency. master-prompt.md loads tier spec + analysis + per-category specs + language example + profile. Minimize unnecessary reads.
2. **Context window consumption** — shorter specs leave more room for the user's actual codebase during analysis. Every line of spec competes with codebase content.
3. **Tier spec size** — `tiers/full.md` lists 25+ files. Each file's spec contributes to total context. Full tier is most at risk of hitting limits.

### Baseline Metrics (Post-c5af3ef)

| Metric | Before | After | Target |
|--------|--------|-------|--------|
| Total spec lines | 917 | 373 | < 400 |
| Redundant files removed | 0 | 5 | 0 remaining |
| Spec files merged | 0 | 3 | as needed |

### What to Measure

- Lines per spec file (flag any spec > 100 lines for review)
- Total plugin file count and size
- Number of `Read file:` directives in a single skill execution path
- Generation time on test projects (qualitative — fast/acceptable/slow)

### Optimization Boundaries

- **Clarity > brevity** — a 50-line spec that Claude follows perfectly beats a 30-line spec that produces ambiguous output
- **Never merge skills** — each skill is a separate cognitive unit with its own trigger
- **Core files stay general** — don't optimize core/ for one skill at the expense of others
- **Registry stays flat** — no nested directories in `registry/examples/` or `registry/profiles/`

### Bloat Detection Signals

- Spec file growing without new functionality
- Duplicate rules across multiple spec files (should be in `core/`)
- Rules that say the same thing in different words
- Examples that don't add calibration value beyond what's already shown
