
**Date:** January 16, 2026  
**Attendance:** All Team Members, Dr Hurst

## Maintainability Concerns
- External APIs reduce maintainability
- Avoid sending data to external services where possible

## Data Evolution Questions
- How often are new pick-lists added?
- How often are new items added?
- Status: To be determined

## Scope Expansion
- Research may extend beyond healthcare pick-lists
- Tool should offer options, not force decisions

## Verification & Correctness
Key questions:
- How do we verify correctness?
- How do we track categorization accuracy?
- How do we evaluate LLM labeling quality?

Suggested ideas:
- Track labeling accuracy explicitly
- Measure error rates over multiple runs
- Define acceptable error thresholds

## Benchmarks for Success
Possible metrics:
- Run system X times, observe Y error rate
- Number of swaps surfaced vs nurse-generated swaps
- Percentage of swaps that surprise nurses
- Reduction in manual nurse adjustments

## Usability Testing
Potential metric:
- Number of manual changes required per session
- Time to reach acceptable consolidation

## Requirements Feedback
- Requirements need more depth
- Break into sub-requirements
- Add quantitative benchmarks to qualitative requirements
- Emphasize that perfect accuracy is not the goal

## Demonstration Guidance
- Clearly define what will be demonstrated
- Ensure demo functionality is real and repeatable

## Related Docs
- [Requirements](../project/requirements.md)
- [Success Metrics](../project/success-metrics.md)
- [Validation Strategy](../technical/validation-strategy.md)
