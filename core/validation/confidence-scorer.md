# Confidence Scoring Methodology

How to compute and interpret confidence scores for generated `.claude/` content.

---

## Score Computation

### Per-File Score

For each generated file:

```
file_confidence = verified_refs / total_refs * 100
```

Where:
- `verified_refs` = count of VERIFIED references (file paths, commands, symbols)
- `total_refs` = count of all extractable references in the file

If a file has 0 references (pure methodology content like protocols), it gets a default score of 100% (no claims to verify).

### Overall Score

```
overall_confidence = sum(all_verified) / sum(all_total) * 100
```

Weighted equally across all files. This gives a single number for the entire generation.

### Adjusted Score (for reporting)

For user-facing reports, group by severity:
- File path references that fail → weight 1.0 (these are clearly wrong)
- Command references that fail → weight 1.0 (these will cause errors)
- Symbol references that fail → weight 0.5 (these might be in generated/external code)

```
adjusted_confidence = (verified + (unverified_symbols * 0.5)) / total * 100
```

## Interpretation Guide

| Score | Interpretation | User Message |
|-------|---------------|--------------|
| 95-100% | Excellent | "High confidence. All critical references verified." |
| 85-94% | Good | "Good confidence. Review the flagged items below." |
| 70-84% | Acceptable | "Moderate confidence. Several references need manual verification." |
| 50-69% | Needs Work | "Low confidence. Re-analyzing sections with unverified references..." |
| <50% | Poor | "Many references could not be verified. Consider re-running with --verbose." |

## Re-Analysis Trigger

When a file scores below 70%:
1. Identify which references failed
2. Read additional source files in the relevant area
3. Regenerate the affected sections with corrected references
4. Re-validate
5. If still below 70% after one retry, flag for manual review and explain what couldn't be found
