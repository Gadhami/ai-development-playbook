---
description: 'Generate a Plandek-style engineering metrics report (opt-in, on-demand only)'
---

# Plandek Engineering Metrics Report

> **This prompt is opt-in.** Only runs when explicitly invoked by the user.

Analyze the repository's recent activity and produce an engineering metrics report.

## Metrics to Gather

### Delivery
- **Cycle Time:** Average time from first commit to merge (for recent PRs).
- **Deployment Frequency:** How often code reaches the main branch.
- **PR Throughput:** Number of PRs merged in the reporting period.

### Quality
- **PR Size:** Average lines changed per PR (flag PRs > 400 lines).
- **Review Time:** Average time from PR creation to first review.
- **Rework Rate:** Percentage of PRs with post-review commits.

### Predictability
- **WIP (Work in Progress):** Number of open PRs / branches at any time.
- **Flow Efficiency:** Time actively worked vs. time waiting (estimated).

## Data Sources

- Git log (`git log --oneline --since="2 weeks ago"`)
- PR history (if accessible via API)
- Branch listing (`git branch -r`)
- Commit frequency distribution

## Report Format

```markdown
# Engineering Metrics Report — [Date Range]

## Summary
[One-paragraph executive summary]

## Delivery Metrics
| Metric | Value | Trend | Assessment |
|---|---|---|---|

## Quality Metrics
| Metric | Value | Trend | Assessment |
|---|---|---|---|

## Recommendations
1. [Actionable recommendation]
2. [Actionable recommendation]

## Raw Data
[Supporting data tables]
```

## Notes

- Use only data available locally or via authorized APIs.
- Flag metrics you cannot calculate with available data.
- Recommendations should be specific and actionable, not generic advice.
