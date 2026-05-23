# PR Review Workflow Prompt

```text
Review the current branch against `master`. Explicitly use the subagents below, wait for all results, and then synthesize a final review.

Assign the review as follows:
- team_architect: check architecture, coupling, public APIs, data flow, dependencies, and non-functional risks.
- business_analyst: check whether the changes drift from requirements, acceptance criteria, or business rules.
- tester: check test gaps, regression risk, edge cases, and reproducible defects.
- software_developer: check code correctness, maintainability, minimal implementation, and potential bugs.
- scrum_master: summarize blocking issues, non-blocking recommendations, owners, and next actions.
- product_owner: assess product acceptance risk and release recommendation based on review evidence.

Use English for every subagent output and for the final consolidated review.

Final output:
1. Overall conclusion
2. Blocking issues
3. Non-blocking recommendations
4. Findings by agent
5. Release risk
6. Suggested next actions
```
