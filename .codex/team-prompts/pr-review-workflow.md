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

Workspace evidence:
- Persist review evidence as Markdown under `workspace/YYYY-MM-DD-pr-review-short-branch-or-task-slug/` before the final answer.
- Use role-oriented review files when applicable, for example `01-architecture/pr-review.md`, `02-requirements/pr-review.md`, `04-implementation/pr-review.md`, `05-quality/pr-review.md`, `06-delivery/pr-review.md`, and `01-product/release-risk.md`.
- Each evidence file must include `Status`, `Owner`, `Created`, `Source task`, and a concise evidence summary.
- Agents with write access may write only their own evidence files. Read-only agents must return evidence to the parent or synthesis agent, which persists it without changing the role's decisions.
- Do not store secrets, tokens, credentials, private keys, `.env` contents, customer-sensitive data, or production-only configuration values in `workspace/`.

Final output:
1. Overall conclusion
2. Blocking issues
3. Non-blocking recommendations
4. Findings by agent
5. Release risk
6. Suggested next actions
7. Artifact index with workspace file paths
```
