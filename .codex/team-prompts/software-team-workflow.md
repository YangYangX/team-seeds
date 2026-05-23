# Software Team Workflow Prompt

Use this prompt inside Codex after the files in this package have been copied into the repository.

```text
Use the software development team subagents defined in this repository under `.codex/agents/` to handle the task below.

Task:
<Paste your product idea, requirement, bug, PR description, or implementation goal here.>

Explicitly create and use these subagents:
1. product_owner: assess product value, scope, priority, release intent, and acceptance goals.
2. business_analyst: break down requirements, business rules, user stories, edge cases, and acceptance criteria.
3. team_architect: assess architecture impact, technical risks, non-functional requirements, and implementation guardrails.
4. software_developer: propose or execute the smallest safe implementation plan after requirements and architecture constraints are clear.
5. tester: create a test plan, validate acceptance criteria, identify regression risk, and provide quality evidence.
6. scrum_master: coordinate handoffs, identify blockers, enforce Definition of Done, and summarize team status and next actions.

Working rules:
- Each subagent must stay within its own role boundary.
- Wait for all required subagent results before producing the final consolidated answer.
- If code changes are required, follow single-writer-multiple-readers: software_developer edits production code by default; tester may edit test files only when useful and safe.
- If frontend pages, UI components, visual design, or interaction states are affected, use `docs/ui-design.md` as the default design system and prefer Tailwind CSS when available without adding an unapproved production dependency.
- Document any frontend design deviations, existing design-system conflicts, or Tailwind constraints in workspace evidence.
- Use English for all role outputs, artifacts, handoff notes, and the final team summary.
- Persist team evidence as Markdown under `workspace/YYYY-MM-DD-short-task-slug/` before the final answer.
- Use this workspace structure when applicable: `00-task.md`, `01-product/`, `02-requirements/`, `03-architecture/`, `04-implementation/`, `05-quality/`, and `06-delivery/`.
- Each evidence file must include `Status`, `Owner`, `Created`, `Source task`, and a concise evidence summary.
- Agents with write access may write only their own evidence files. Read-only agents must return evidence to the parent or synthesis agent, which persists it without changing the role's decisions.
- Do not store secrets, tokens, credentials, private keys, `.env` contents, customer-sensitive data, or production-only configuration values in `workspace/`.
- Final output must include: product conclusion, requirements conclusion, architecture conclusion, implementation plan or implementation result, test plan or validation result, risks, blockers, next actions, and an artifact index with workspace file paths.
```
