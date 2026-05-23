# Autonomous Delivery Workflow Prompt

Use this prompt inside Codex when you want the team to move from an idea, requirement, bug, or release goal to the furthest safe delivery point with minimal human prompting.

```text
Use the software development team subagents defined in this repository under `.codex/agents/` to handle the task below autonomously.

Task:
<Paste your product idea, requirement, bug, release goal, or implementation goal here.>

Delivery mode:
<Choose one: plan-only | implement | production-readiness | release-support. If omitted, default to implement.>

Project context:
- Repository type: <greenfield | existing application | library | service | unknown>
- Target users: <users/personas, or Unknown>
- Release target: <internal beta | public release | production patch | planning only | unknown>
- Runtime/deployment environment: <known environment, or Unknown>
- Data sensitivity: <none | internal | customer data | regulated | unknown>
- Frontend/UI involved: <yes | no | unknown>
- Frontend stack: <Tailwind CSS | other stack | unknown>
- Existing constraints: <technical/product constraints, or Unknown>

Scope boundaries:
- In scope: <must-have outcomes>
- Out of scope: <explicit exclusions>
- Preferred first increment: <smallest valuable slice, or let the team propose>

Quality expectations:
- Tests: <expected test level, or discover from repository>
- Validation: <commands if known, or discover from repository>
- Risk tolerance: <low | medium | high, with reason>
- Release safety: <internal only | production users | public launch | unknown>

Frontend/UI expectations:
- If frontend pages, UI components, visual design, or interaction states are affected, use `docs/ui-design.md` as the default design system.
- Prefer Tailwind CSS for styling when the project already uses Tailwind or can use it without adding an unapproved production dependency.
- Reconcile `docs/ui-design.md` with any existing project design system and document necessary deviations.
- Validate responsive behavior, accessibility, and visual consistency against `docs/ui-design.md` when frontend work is implemented.

Autonomy goal:
- Continue through the workflow without asking for step-by-step prompts.
- Make reasonable assumptions when safe, label them clearly, and persist them as evidence.
- Batch open questions into the smallest possible number of decision checkpoints.
- Stop only for the approval gates listed below, a true blocker, or when the requested delivery mode is complete.

Explicitly create and use these subagents when their role is relevant:
1. product_owner: product value, scope, priority, release intent, and acceptance goals.
2. business_analyst: requirements, business rules, user stories, edge cases, and acceptance criteria.
3. team_architect: architecture impact, technical risks, non-functional requirements, and implementation guardrails.
4. software_developer: smallest safe implementation plan and focused code changes.
5. tester: test plan, validation, regression risk, bug reports, and quality evidence.
6. scrum_master: handoffs, blockers, Definition of Done, status, and next actions.

Default execution loop:
1. Intake and workspace setup
   - Create `workspace/YYYY-MM-DD-short-task-slug/`.
   - Persist the original task in `00-task.md`.
   - Identify delivery mode, assumptions, known constraints, and immediate blockers.

2. Product and requirements discovery
   - Use product_owner and business_analyst to define MVP scope, out-of-scope items, user stories, business rules, edge cases, acceptance criteria, and traceability.
   - Persist evidence under `01-product/` and `02-requirements/`.
   - If scope is too broad, reduce to the smallest valuable increment and label deferred items.

3. Architecture guardrails
   - Use team_architect to inspect the repository and define target design, affected components, alternatives, tradeoffs, risks, non-functional requirements, and implementation guardrails.
   - If frontend/UI is affected, include design-system guardrails from `docs/ui-design.md` and the project's styling stack.
   - Persist evidence under `03-architecture/`.
   - Escalate only if a new production dependency, migration, public API change, security boundary change, or broad refactor is required.

4. Delivery slicing
   - Use scrum_master with the relevant agents to split work into small slices with one accountable owner per item.
   - Persist Definition of Done, handoff notes, blockers, and next actions under `06-delivery/`.

5. Implementation loop
   - If delivery mode is `plan-only`, stop after producing the plan, evidence, risks, and next actions.
   - If delivery mode is `implement`, `production-readiness`, or `release-support`, use software_developer to implement the next safe slice.
   - Keep changes focused and aligned with existing repository patterns.
   - If frontend/UI is affected, implement against `docs/ui-design.md` and prefer Tailwind CSS when available.
   - Do not introduce new production dependencies without explicit approval.
   - Persist implementation plan, changed files, key decisions, validation commands, and implementation summary under `04-implementation/`.

6. Validation loop
   - Use tester to validate each implemented slice against acceptance criteria.
   - Run relevant available validation commands.
   - Persist test plan, validation results, bug reports, coverage gaps, and release-quality recommendation under `05-quality/`.
   - If blocking defects are found, send them back to software_developer for a focused fix, then validate again.
   - Continue this fix-and-validate loop until blocking defects are resolved, validation is blocked, or two focused fix attempts fail.

7. Internal review
   - Use the relevant review roles to check product acceptance risk, requirements drift, architecture risk, code correctness, test gaps, and Definition of Done.
   - Persist review evidence in the appropriate workspace subdirectories.

8. Production readiness
   - If delivery mode is `production-readiness` or `release-support`, prepare production-readiness evidence covering deployment, configuration, secrets by name only, database migration, observability, security, accessibility, performance, rollback, and post-release validation.
   - Persist production-readiness and deployment-runbook evidence under `06-delivery/`.
   - Do not execute production deployment, destructive migrations, production data changes, or external side-effect operations unless the user explicitly requested and approved them.

9. Final synthesis
   - Produce a concise final answer with product conclusion, requirements conclusion, architecture conclusion, implementation result or plan, validation result, production-readiness status when applicable, risks, blockers, next actions, and artifact index.
   - Include every workspace artifact path with owner and status.

Approval gates:
- Ask for approval before adding a new production dependency.
- Ask for approval before adding Tailwind CSS or another styling dependency if the target project does not already use it.
- Ask for approval before making destructive filesystem, database, deployment, or git operations.
- Ask for approval before changing public API compatibility, authentication, authorization, data retention, or production configuration semantics.
- Ask for approval before using or printing secrets, credentials, tokens, private keys, `.env` contents, or customer-sensitive data. Prefer not to handle these values at all.
- Ask a concise decision question if product scope, legal/compliance behavior, billing behavior, or user-impacting tradeoff cannot be safely assumed.
- If approval is needed, batch all approval questions into one checkpoint whenever possible.

Workspace evidence rules:
- Persist team evidence as Markdown under `workspace/YYYY-MM-DD-short-task-slug/` before the final answer.
- Use this structure when applicable: `00-task.md`, `01-product/`, `02-requirements/`, `03-architecture/`, `04-implementation/`, `05-quality/`, and `06-delivery/`.
- Each evidence file must include `Status`, `Owner`, `Created`, `Source task`, and a concise evidence summary.
- Agents with write access may write only their own evidence files.
- Read-only agents must return evidence to the parent or synthesis agent, which persists it without changing the role's decisions.
- Do not store secrets, tokens, credentials, private keys, `.env` contents, customer-sensitive data, or production-only configuration values in `workspace/`.

Working rules:
- Each subagent must stay within its own role boundary.
- Wait for required subagent results before making cross-role conclusions.
- Follow single-writer-multiple-readers: software_developer edits production code by default; tester may edit test files only when useful and safe.
- Use English for all role outputs, artifacts, handoff notes, and the final team summary.
- Prefer completing the requested delivery mode over asking for more prompts.
- When blocked, explain the blocker, evidence, attempted steps, and the smallest user decision needed to continue.
```
