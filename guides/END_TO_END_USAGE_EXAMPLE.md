# End-to-End Agent Team Usage Example

This guide is a full example of how to use the Codex software team seed from the first product idea to a production release.

It is intentionally detailed. The default interactive path is team router mode: start with `.codex/team-prompts/team-router-session.md` once, then talk to Codex as if you are giving requests or feedback to a software team. For a large end-to-end delivery request, use `.codex/team-prompts/autonomous-delivery-workflow.md` and let Codex run the stages. The later stage-by-stage prompts are a manual and vocabulary reference for when you want to intervene, debug, or run one role directly.

## Quick Mental Model

This seed gives Codex a small software team:

- `product_owner` decides product value, scope, priority, acceptance intent, and release risk.
- `business_analyst` turns product intent into testable requirements, business rules, edge cases, and acceptance criteria.
- `team_architect` maps technical impact, architecture constraints, non-functional requirements, risks, and implementation guardrails.
- `software_developer` makes focused code changes inside the accepted requirements and architecture guardrails.
- `tester` validates acceptance criteria, creates test plans, reports bugs, and gives release-quality evidence.
- `scrum_master` coordinates handoffs, blockers, owners, Definition of Done, and next actions.

Important behavior:

- In team router mode, the parent agent routes non-trivial follow-up messages to the smallest relevant set of subagents automatically.
- Simple questions, direct shell-command requests, and narrow clarifications can still be answered directly.
- Workflow prompts under `.codex/team-prompts/` are reusable meeting agendas.
- Agent definitions under `.codex/agents/` are role instructions.
- `AGENTS.md` is the project-level operating agreement.
- `workspace/` is a local, untracked evidence folder.

## Example Scenario

Product idea:

```text
Build a SaaS tool that helps small operations teams manage recurring compliance checklists.

Users should be able to create checklist templates, schedule recurring checklist runs, assign owners,
mark items complete, see overdue work, and export a weekly compliance report.
```

The goal is to move from this rough idea to a production-ready MVP.

## Default Low-Interaction Workflow

For production work, do not feed Codex a long chain of small prompts. Start with `team-router-session.md` once for the interactive session, then either describe follow-up work naturally or start one autonomous delivery prompt that gives the team enough context to make safe progress. Let it continue until the selected delivery mode is complete, a quality gate fails, or an approval gate is reached.

The goal is not "no human judgment." The goal is "no human project-management babysitting." Humans still approve high-impact decisions, production side effects, compatibility changes, and unclear business tradeoffs.

Use this workflow for:

- New feature delivery in an existing product.
- Greenfield MVP planning and first implementation slices.
- Bug reproduction, fix, and validation.
- Release preparation and production-readiness review.
- Internal beta rollout support.

Do not use it as a blind autopilot for:

- Production deployment without an explicit deployment request.
- Destructive database or filesystem operations.
- Secret handling.
- Legal, compliance, billing, or user-impacting policy decisions that have not been defined.
- Large architecture rewrites without approval.

### Session Startup Prompt

At the start of an interactive session, run:

```text
Read `.codex/team-prompts/team-router-session.md` and execute it.
```

After that, you can type follow-up messages naturally:

```text
Users say the weekly CSV export has the wrong date range. Please investigate and fix it.
```

The parent agent should route the work to the relevant roles, wait for required outputs, perform the allowed implementation or validation work, and return one coherent result.

### Production-Ready Startup Prompt

Use a prompt shaped like this. Fill in the sections you know, and leave unknown sections as `Unknown` rather than inventing details.

```text
Read `.codex/team-prompts/autonomous-delivery-workflow.md` and execute it.

Task:
Build a SaaS tool that helps small operations teams manage recurring compliance checklists.
Users should be able to create checklist templates, schedule recurring checklist runs, assign owners,
mark items complete, see overdue work, and export a weekly compliance report.

Delivery mode:
implement

Project context:
- Repository type: Existing application
- Target users: Small operations teams and operations managers
- Release target: Internal beta first, not public launch
- Runtime/deployment environment: Unknown
- Data sensitivity: Compliance workflow metadata; do not expose customer data
- Frontend/UI involved: Yes
- Frontend stack: Tailwind CSS if present, otherwise discover from repository
- Existing constraints: Follow current repository patterns and avoid new production dependencies unless approved

Scope boundaries:
- In scope: checklist templates, weekly scheduled runs, assignment, completion, overdue status, weekly CSV export
- Out of scope for the first slice: Slack notifications, SSO, mobile app, complex approvals, custom recurrence beyond weekly
- Prefer the smallest valuable increment if the full scope is too large

Quality expectations:
- Add or update targeted tests where feasible
- Run relevant validation commands discovered from the repository
- If validation cannot run, explain why and provide the next best validation
- Treat failing tests as blockers unless clearly unrelated and documented

Frontend/UI expectations:
- Use `docs/ui-design.md` as the default visual design guide for pages and components
- Prefer Tailwind CSS when the project already uses it or can use it without an unapproved production dependency
- Document any conflict with an existing project design system before implementation
- Validate responsive behavior, accessibility, and interaction states for frontend changes

Autonomy and approval:
- Continue autonomously through discovery, planning, implementation, validation, review, and evidence writing
- Make safe assumptions and label them
- Batch open questions into one approval checkpoint when possible
- Stop for approval before new production dependencies, destructive operations, public API/auth/data-retention compatibility changes, production deployment, or secret handling
- Do not print secret values

Workspace:
- Persist evidence under `workspace/2026-05-23-ops-checklist-mvp/`
- Include an artifact index in the final response

Use the project subagents under `.codex/agents/`.
Follow `AGENTS.md`.
```

### Choosing Delivery Mode

- `plan-only`: stop after product, requirements, architecture, test plan, delivery plan, risks, and artifact index.
- `implement`: continue through planning, focused implementation slices, tests, validation, internal review, and artifact index.
- `production-readiness`: continue through implementation if needed, validation, production-readiness review, deployment runbook, rollback plan, and post-release validation plan. Do not execute production deployment without explicit approval.
- `release-support`: help prepare, review, commit, release, or validate a release. Do not perform destructive or external side-effect operations without explicit approval.

Production guidance:

- Use `plan-only` when the idea is early, budget/scope is unclear, or the architecture impact may be large.
- Use `implement` for normal feature and bug-fix delivery on a branch.
- Use `production-readiness` when code is mostly ready and the main risk is release safety.
- Use `release-support` when you need help preparing release notes, commit messages, review evidence, deployment runbooks, or post-release validation.

### What Codex Should Do Without More Prompts

After the startup prompt, Codex should independently:

- Inspect the repository before proposing implementation details.
- Create the workspace folder and `00-task.md`.
- Ask product and requirements agents to define scope and acceptance criteria.
- Ask the architect to define implementation guardrails.
- Split work into small delivery slices.
- Implement the next safe slice if the delivery mode allows implementation.
- Add or update tests where feasible.
- Run relevant validation commands.
- Loop once or twice on blocking defects found by `tester`.
- Persist evidence in `workspace/`.
- Produce a final artifact index with owners and status.

### When Codex Should Stop And Ask

The workflow should stop for a batched approval checkpoint when it needs to:

- Add or upgrade a production dependency.
- Run destructive commands.
- Change public API compatibility.
- Change authentication, authorization, billing, data retention, or compliance semantics.
- Execute production deployment or production migrations.
- Access, print, transform, or store secrets or customer-sensitive data.
- Choose between product tradeoffs with meaningful user or business impact.
- Continue after repeated validation failure where the next fix is no longer obvious.

Good approval checkpoint:

```text
Approval checkpoint:
1. Add `bullmq` for background scheduling, or keep MVP weekly run generation inside the existing scheduled job mechanism if present?
2. Add a database migration for checklist templates and runs?
3. Defer CSV export formatting customization to the next release?

Recommendation:
Use existing scheduled job mechanism if available, approve the migration after review, and defer CSV customization.
```

Bad approval checkpoint:

```text
What should I do next?
```

### Expected Production Artifacts

A production-grade autonomous run should leave evidence similar to:

```text
workspace/2026-05-23-ops-checklist-mvp/
  00-task.md
  01-product/product-brief.md
  01-product/acceptance-intent.md
  01-product/release-risk.md
  02-requirements/requirements.md
  02-requirements/acceptance-criteria.md
  02-requirements/traceability.md
  03-architecture/architecture-review.md
  03-architecture/implementation-guardrails.md
  04-implementation/implementation-plan.md
  04-implementation/implementation-summary.md
  04-implementation/changed-files.md
  05-quality/test-plan.md
  05-quality/validation-results.md
  05-quality/release-quality.md
  06-delivery/dod-checklist.md
  06-delivery/handoff-summary.md
  06-delivery/next-actions.md
  07-service-manual/service-manual.md
```

For production-readiness or release-support mode, also expect:

```text
workspace/2026-05-23-ops-checklist-mvp/
  06-delivery/production-readiness.md
  06-delivery/deployment-runbook.md
  06-delivery/rollback-plan.md
  06-delivery/post-release-validation.md
```

### Final Response Contract

The final response should be short and operational:

```text
Overall status:
Implemented Slice 1 and validated it. Production deployment was not executed.

Product:
MVP scope remains checklist templates, weekly runs, assignment, completion, overdue status, and weekly CSV export.

Implementation:
Changed files:
- `src/...`
- `tests/...`

Validation:
- `npm test -- checklist`: PASS
- `npm run typecheck`: PASS

Approval gates:
- Database migration requires human review before production.

Risks:
- UI automation is not present in this repository.

Next actions:
1. Review migration.
2. Continue with weekly run generation slice.
3. Run production-readiness mode before deployment.

Artifact index:
- `workspace/2026-05-23-ops-checklist-mvp/00-task.md`
- `workspace/2026-05-23-ops-checklist-mvp/05-quality/validation-results.md`
- `workspace/2026-05-23-ops-checklist-mvp/07-service-manual/service-manual.md`
```

The rest of this guide explains what the autonomous workflow is doing internally. You only need the individual stage prompts when you want manual control over one part of the process.

## Repository Setup

Copy the team seed into the target project:

```bash
cp -r .codex AGENTS.md .gitignore workspace /path/to/target-repo/
mkdir -p /path/to/target-repo/docs
cp docs/commit-message-standard.md docs/ui-design.md /path/to/target-repo/docs/
mkdir -p /path/to/target-repo/guides
cp guides/TEAM_SETUP.md guides/END_TO_END_USAGE_EXAMPLE.md /path/to/target-repo/guides/
cd /path/to/target-repo
codex
```

If the target repository already has a `.gitignore`, `docs/`, or `guides/` directory, merge the relevant files instead of replacing existing project files.

Directory intent:

- `.codex/`: Codex configuration, custom agents, and workflow prompts.
- `docs/`: Runtime-readable team standards and project guidance that agents should use during work, such as commit and UI design standards.
- `guides/`: Human-facing setup and usage manuals for the repository owner. Agents should not use these files as runtime requirements unless the user explicitly asks for guide/tutorial/documentation work.
- `workspace/`: Local, untracked team evidence.

Permission behavior:

- `.codex/config.toml` sets `approval_policy = "never"` so routine shell commands do not block on repeated CLI permission prompts.
- `.codex/config.toml` sets `sandbox_mode = "workspace-write"` so commands remain sandboxed to the workspace by default.
- The autonomous workflow still stops for user approval before destructive operations, production deployment, new production dependencies, public compatibility changes, production configuration changes, or secret handling.
- If you prefer interactive command approvals, change `approval_policy` to `on-request`.

Expected target layout:

```text
<repo>/
  .gitignore
  AGENTS.md
  docs/
    commit-message-standard.md
    ui-design.md
  guides/
    TEAM_SETUP.md
    END_TO_END_USAGE_EXAMPLE.md
  workspace/
    .gitignore
  .codex/
    config.toml
    agents/
      product-owner.toml
      business-analyst.toml
      team-architect.toml
      software-developer.toml
      tester.toml
      scrum-master.toml
    team-prompts/
      team-router-session.md
      autonomous-delivery-workflow.md
      software-team-workflow.md
      pr-review-workflow.md
```

## Workspace Convention

Every team workflow should create a local evidence folder:

```text
workspace/YYYY-MM-DD-short-task-slug/
```

Example:

```text
workspace/2026-05-23-ops-checklist-mvp/
```

Recommended artifact layout:

```text
workspace/2026-05-23-ops-checklist-mvp/
  00-task.md
  01-product/
    product-brief.md
    acceptance-intent.md
    release-risk.md
  02-requirements/
    requirements.md
    acceptance-criteria.md
    traceability.md
  03-architecture/
    architecture-review.md
    implementation-guardrails.md
    adr-draft.md
  04-implementation/
    implementation-plan.md
    implementation-summary.md
    changed-files.md
  05-quality/
    test-plan.md
    validation-results.md
    bug-reports.md
    release-quality.md
  06-delivery/
    dod-checklist.md
    handoff-summary.md
    production-readiness.md
    next-actions.md
  07-service-manual/
    service-manual.md
```

Each evidence file should include:

```text
Status: draft | reviewed | accepted | superseded
Owner: <agent role>
Created: YYYY-MM-DD
Source task: <short description or prompt reference>
Evidence summary: <brief summary>
```

Do not store secrets, private keys, `.env` contents, tokens, customer-sensitive data, or production-only configuration values in `workspace/`.

`workspace/` is intentionally ignored by git except for `workspace/.gitignore`.

### Service Manual Draft

For product, feature, implementation, production-readiness, or release-support workflows, the team maintains a synthesized service manual draft:

```text
workspace/YYYY-MM-DD-short-task-slug/07-service-manual/service-manual.md
```

Each agent contributes only its role-owned content:

- `product_owner`: service purpose, users, scope, release intent, and product risks.
- `business_analyst`: workflows, business rules, glossary, edge cases, and acceptance behavior.
- `team_architect`: architecture, components, integrations, data flows, non-functional requirements, and operational constraints.
- `software_developer`: implementation notes, modules/files, APIs, commands, configuration names only, local development, and technical limitations.
- `tester`: validation strategy, evidence summary, known defects, quality risks, troubleshooting signals, and release recommendation.
- `scrum_master`: delivery status, owners, handoffs, Definition of Done, release checklist, open decisions, and next actions.

Role ownership controls source responsibility, not final document structure. The parent or synthesis agent edits these contributions into one fluent manual with a single editorial voice, consistent terminology, consistent heading style, and a service-centered reading order. Keep the workspace copy as a draft until you explicitly approve promotion into tracked docs such as `docs/service-manual.md`.

## Stage 1: Start From The Raw Idea

Use this manual stage prompt only when you want Codex to stop after discovery instead of continuing through implementation. For normal low-interaction work, use the default autonomous prompt above.

Prompt:

```text
Read `.codex/team-prompts/software-team-workflow.md` and execute it.

Task:
Build a SaaS tool that helps small operations teams manage recurring compliance checklists.
Users should be able to create checklist templates, schedule recurring checklist runs, assign owners,
mark items complete, see overdue work, and export a weekly compliance report.

Use the project subagents defined under `.codex/agents/`.
Follow `AGENTS.md`.
Do not write production code yet. First produce product, requirements, architecture, test, and delivery evidence.
Persist evidence under `workspace/2026-05-23-ops-checklist-mvp/`.
```

Expected team behavior:

- `product_owner` defines the product problem, target users, MVP scope, out-of-scope items, priorities, and acceptance intent.
- `business_analyst` defines user stories, functional requirements, business rules, edge cases, and acceptance criteria.
- `team_architect` inspects the repository and identifies architecture impact, data model needs, integration points, and guardrails.
- `software_developer` proposes the smallest implementation plan but does not write code yet.
- `tester` creates a test strategy and maps it to acceptance criteria.
- `scrum_master` identifies owners, blockers, DoD gaps, and next actions.

Expected workspace evidence:

```text
workspace/2026-05-23-ops-checklist-mvp/
  00-task.md
  01-product/product-brief.md
  01-product/acceptance-intent.md
  02-requirements/requirements.md
  02-requirements/acceptance-criteria.md
  02-requirements/traceability.md
  03-architecture/architecture-review.md
  03-architecture/implementation-guardrails.md
  04-implementation/implementation-plan.md
  05-quality/test-plan.md
  06-delivery/dod-checklist.md
  06-delivery/next-actions.md
  07-service-manual/service-manual.md
```

Example final response shape:

```text
Product conclusion:
The MVP should focus on recurring checklist templates, scheduled runs, assignees, completion state, overdue visibility, and weekly CSV export.

Requirements conclusion:
The first release needs checklist templates, recurrence rules, checklist run generation, item completion, owner assignment, overdue status, and report export.

Architecture conclusion:
Use existing application patterns. Add the smallest domain model needed for templates, runs, items, assignments, and exports. Avoid new production dependencies unless approved.

Implementation plan:
Slice 1: data model and API for templates.
Slice 2: scheduled run generation.
Slice 3: completion workflow and overdue view.
Slice 4: weekly report export.

Validation plan:
Unit tests for recurrence rules, API tests for checklist CRUD, integration tests for run generation, and UI tests for completion flow if a UI test framework exists.

Artifact index:
- workspace/2026-05-23-ops-checklist-mvp/01-product/product-brief.md
- workspace/2026-05-23-ops-checklist-mvp/02-requirements/acceptance-criteria.md
- workspace/2026-05-23-ops-checklist-mvp/03-architecture/implementation-guardrails.md
- workspace/2026-05-23-ops-checklist-mvp/05-quality/test-plan.md
```

## Stage 2: Refine Product Scope

If the first pass produces too much scope, ask for a product and requirements refinement before implementation.

Prompt:

```text
Use `product_owner` and `business_analyst` to refine the MVP.

Inputs:
- workspace/2026-05-23-ops-checklist-mvp/01-product/product-brief.md
- workspace/2026-05-23-ops-checklist-mvp/02-requirements/requirements.md

Goals:
- Separate MVP, next release, and out-of-scope items.
- Identify assumptions and open questions.
- Make all acceptance criteria observable.
- Persist updated evidence under the same workspace folder.

Do not write code.
```

Example output:

```text
MVP:
- Create checklist templates.
- Schedule weekly checklist runs.
- Assign each run to one owner.
- Complete checklist items.
- Show overdue runs.
- Export weekly CSV report.

Next release:
- Multiple owners per item.
- Slack notifications.
- Audit log.
- Custom recurrence rules beyond weekly.

Out of scope:
- Mobile app.
- Enterprise SSO.
- Complex approval workflows.
```

Decision rule:

- If a feature is not required to prove the MVP value, defer it.
- If a business rule is unclear, label it as an assumption or ask the user.
- If a requirement cannot be tested, rewrite it.

## Stage 3: Produce Implementation-Ready Requirements

Prompt:

```text
Use `business_analyst` to turn the accepted MVP scope into implementation-ready user stories.

Inputs:
- workspace/2026-05-23-ops-checklist-mvp/01-product/product-brief.md
- workspace/2026-05-23-ops-checklist-mvp/01-product/acceptance-intent.md

Output:
- User stories
- Functional requirements
- Business rules
- Edge cases
- Acceptance criteria
- Requirement traceability matrix

Persist evidence under `workspace/2026-05-23-ops-checklist-mvp/02-requirements/`.
Do not write code.
```

Example user story:

```text
As an operations manager,
I want to create a weekly checklist template,
so that recurring compliance tasks can be generated consistently.
```

Example acceptance criteria:

```text
Given an operations manager is creating a checklist template
When they provide a name and at least one checklist item
Then the system saves the template and shows it in the template list

Given a checklist template has no items
When the operations manager tries to save it
Then the system rejects the template and explains that at least one item is required
```

Traceability example:

```text
Product goal: Reduce missed recurring compliance tasks
Requirement: Generate weekly checklist runs from active templates
Acceptance criterion: Weekly run appears for each active template on the scheduled date
Test idea: Freeze time, create active template, run scheduler, assert one run is generated
```

## Stage 4: Architecture Review And Guardrails

Prompt:

```text
Use `team_architect` to inspect the repository and propose the smallest safe architecture for the accepted MVP.

Inputs:
- workspace/2026-05-23-ops-checklist-mvp/02-requirements/requirements.md
- workspace/2026-05-23-ops-checklist-mvp/02-requirements/acceptance-criteria.md

Output:
- Current-state evidence with file paths
- Target design
- Affected modules/files
- Data model/API/UI impact
- Alternatives considered
- Technical risks and mitigations
- Non-functional requirements
- Implementation guardrails
- ADR draft if needed

Persist evidence under `workspace/2026-05-23-ops-checklist-mvp/03-architecture/`.
Do not write code.
```

Architecture output should cite repository evidence:

```text
Current-state evidence:
- `src/routes/` contains existing page routing.
- `src/db/schema.ts` defines database tables.
- `src/lib/auth.ts` exposes the current user context.

Target design:
- Add checklist template, checklist item, checklist run, and run item tables.
- Add API handlers using existing request validation conventions.
- Reuse existing auth checks for owner-scoped data.
- Add weekly report export as a server-side CSV endpoint.

Guardrails:
- Do not add a new scheduler dependency without approval.
- Keep recurrence to weekly for MVP.
- Do not change existing auth semantics.
- Add migration rollback notes if the project supports rollback.
```

When to create an ADR:

- New production dependency.
- New database persistence model with long-term consequences.
- Public API change.
- Deployment, security, or operational decision that future maintainers must understand.

## Stage 5: Slice The Work

Prompt:

```text
Use `scrum_master`, with inputs from `product_owner`, `business_analyst`, `team_architect`, `software_developer`, and `tester`, to split the MVP into safe implementation slices.

Inputs:
- workspace/2026-05-23-ops-checklist-mvp/01-product/product-brief.md
- workspace/2026-05-23-ops-checklist-mvp/02-requirements/traceability.md
- workspace/2026-05-23-ops-checklist-mvp/03-architecture/implementation-guardrails.md

Output:
- Work items
- One accountable owner per item
- Dependencies
- Blockers
- Definition of Done checklist
- Recommended sequence

Persist evidence under `workspace/2026-05-23-ops-checklist-mvp/06-delivery/`.
Do not write code.
```

Example slices:

```text
Slice 1: Checklist templates
Owner: software_developer
Definition of Done:
- Template can be created, listed, updated, and deleted.
- Template requires a name and at least one item.
- Tests cover valid and invalid template creation.

Slice 2: Weekly run generation
Owner: software_developer
Definition of Done:
- Active weekly templates generate one run per week.
- Duplicate runs are not created for the same template/week.
- Tests cover duplicate prevention.

Slice 3: Completion and overdue view
Owner: software_developer
Definition of Done:
- Assignee can mark items complete.
- Run status updates when all items are complete.
- Overdue runs appear in the overdue view.

Slice 4: Weekly CSV export
Owner: software_developer
Definition of Done:
- Report includes completed, incomplete, and overdue runs.
- CSV format is stable and documented.
- Tests cover export content.
```

## Stage 6: Implement The First Slice

Use implementation only after scope, requirements, and guardrails are clear.

Prompt:

```text
Use `software_developer` to implement Slice 1: Checklist templates.

Inputs:
- workspace/2026-05-23-ops-checklist-mvp/02-requirements/acceptance-criteria.md
- workspace/2026-05-23-ops-checklist-mvp/03-architecture/implementation-guardrails.md
- workspace/2026-05-23-ops-checklist-mvp/06-delivery/dod-checklist.md

Rules:
- Make the smallest focused code change.
- Follow existing repository patterns.
- Do not introduce new production dependencies without explicit approval.
- Add or update targeted tests where feasible.
- Run relevant validation commands.
- Persist implementation evidence under `workspace/2026-05-23-ops-checklist-mvp/04-implementation/`.
```

Expected developer evidence:

```text
Implementation Summary:
- Added checklist template persistence.
- Added API handler for template creation and listing.
- Added validation that a template must include at least one item.

Files Changed:
- `src/db/schema.ts`
- `src/routes/checklistTemplates.ts`
- `src/routes/checklistTemplates.test.ts`

Validation Commands and Results:
- `npm test -- checklistTemplates`: PASS
- `npm run typecheck`: PASS

Known Limitations:
- Recurrence generation is not implemented in this slice.
```

Good implementation behavior:

- Read relevant files before editing.
- Keep changes traceable to acceptance criteria.
- Add tests for changed behavior.
- Stop and escalate if requirements conflict with architecture guardrails.
- Report validation commands exactly.

Bad implementation behavior:

- Building all MVP slices at once.
- Introducing a scheduler, queue, or notification service without approval.
- Refactoring unrelated modules.
- Declaring success without running tests or explaining why tests could not run.

## Stage 7: Validate The Slice

Prompt:

```text
Use `tester` to validate Slice 1 against the acceptance criteria.

Inputs:
- workspace/2026-05-23-ops-checklist-mvp/02-requirements/acceptance-criteria.md
- workspace/2026-05-23-ops-checklist-mvp/04-implementation/implementation-summary.md

Output:
- Acceptance criteria coverage
- Test cases
- Validation commands and results
- Bugs found
- Coverage gaps
- Release-quality recommendation for this slice

Persist evidence under `workspace/2026-05-23-ops-checklist-mvp/05-quality/`.
```

Example quality evidence:

```text
Acceptance Criteria Coverage:
- Template creation with valid name and items: covered by `checklistTemplates.test.ts`
- Template creation without items: covered by `checklistTemplates.test.ts`
- Template appears in template list after creation: covered by API integration test

Validation Commands and Results:
- `npm test -- checklistTemplates`: PASS
- `npm run typecheck`: PASS

Bugs Found:
- None.

Coverage Gaps:
- No browser-level UI test exists for template creation.

Release Quality Recommendation:
- Ready for merge as Slice 1 if UI coverage is not required for this repository.
```

If bugs are found, the tester should produce a reproducible bug report:

```text
Bug: Empty template can be saved through API
Severity: Blocking
Steps:
1. Send POST `/api/checklist-templates` with name and empty items array.
2. Observe 201 response.
Expected:
The API should reject the request with a validation error.
Actual:
The API creates an invalid template.
Suspected area:
`src/routes/checklistTemplates.ts`
```

Then send the bug back to `software_developer` for a focused fix.

## Stage 8: Repeat Slices Until MVP Is Complete

For each slice:

1. Confirm scope and acceptance criteria.
2. Confirm architecture guardrails still apply.
3. Implement the smallest focused change.
4. Add or update tests.
5. Run validation.
6. Persist evidence.
7. Update the artifact index.

Recommended prompt:

```text
Continue the MVP using the next delivery slice from `workspace/2026-05-23-ops-checklist-mvp/06-delivery/dod-checklist.md`.

Use only the agents needed for this slice:
- business_analyst if acceptance criteria need clarification
- team_architect if architecture guardrails may change
- software_developer for implementation
- tester for validation
- scrum_master for DoD and handoff updates

Persist updated evidence under the existing workspace folder.
```

You do not need to run every agent for every tiny change. Use the full team for ambiguous or high-risk work, and use targeted roles for focused work.

## Stage 9: Review The Branch Before Merge

When the branch is ready, run the PR review workflow.

Prompt:

```text
Read `.codex/team-prompts/pr-review-workflow.md` and execute it.

Review the current branch against `master`.

Important:
- Use the project subagents defined under `.codex/agents/`.
- Wait for all required subagent results.
- Do not make code changes.
- Produce the final consolidated review in the format required by the workflow.
- Persist review evidence under `workspace/2026-05-23-pr-review-ops-checklist-mvp/`.
```

Expected review responsibilities:

```text
team_architect:
- Architecture, coupling, public APIs, data flow, dependencies, NFR risks

business_analyst:
- Requirements drift, acceptance criteria coverage, business-rule mismatches

tester:
- Test gaps, regression risk, edge cases, reproducible defects

software_developer:
- Code correctness, maintainability, minimal implementation, potential bugs

scrum_master:
- Blocking issues, non-blocking recommendations, owners, next actions

product_owner:
- Product acceptance risk and release recommendation
```

Final review shape:

```text
Overall conclusion:
Ready to merge after one non-blocking documentation update.

Blocking issues:
- None.

Non-blocking recommendations:
- Add an example CSV output to tracked docs before public release.

Findings by agent:
- team_architect: no new dependency risk; data model aligns with guardrails.
- business_analyst: all MVP acceptance criteria are represented.
- tester: unit and integration tests pass; UI automation remains a coverage gap.
- software_developer: implementation is focused and follows local patterns.
- scrum_master: DoD satisfied except optional docs improvement.
- product_owner: MVP release risk is acceptable for internal beta.

Artifact index:
- workspace/2026-05-23-pr-review-ops-checklist-mvp/01-architecture/pr-review.md
- workspace/2026-05-23-pr-review-ops-checklist-mvp/05-quality/pr-review.md
- workspace/2026-05-23-pr-review-ops-checklist-mvp/06-delivery/pr-review.md
```

## Stage 10: Commit And Push

Use Conventional Commits from `docs/commit-message-standard.md`.

Example commits:

```text
feat(checklists): add checklist template management
feat(checklists): generate weekly checklist runs
fix(checklists): prevent duplicate weekly runs
test(checklists): cover overdue report export
docs(checklists): document weekly report CSV format
```

Prompt for Codex:

```text
Based on `docs/commit-message-standard.md`, generate a Conventional Commits style commit message for the current staged changes.

Requirements:
- Use the format: <type>[optional scope][optional !]: <description>
- Decide whether a body and footer are needed.
- If the changes contain multiple unrelated intentions, recommend splitting them into multiple commits.
```

Before pushing:

```bash
git status --short
git diff --cached --stat
git commit -m "feat(checklists): add checklist template management"
git push origin <branch>
```

Do not commit `workspace/` evidence by default. Promote durable decisions into tracked docs only when they should become part of the repository.

## Stage 11: Production Readiness Review

Run a production readiness review before the first real deployment.

Prompt:

```text
Use the project subagents to prepare a production readiness review for the operations checklist MVP.

Inputs:
- workspace/2026-05-23-ops-checklist-mvp/
- Current repository state

Focus:
- Deployment
- Configuration
- Secrets
- Database migration
- Observability
- Security
- Performance
- Accessibility
- Backup and rollback
- Post-release validation

Do not change code unless a blocking production-readiness issue requires it.
Persist evidence under `workspace/2026-05-23-production-readiness-ops-checklist-mvp/`.
```

Production readiness checklist:

```text
Product:
- Release intent is clear.
- MVP users and rollout audience are known.
- Acceptance criteria are validated.

Requirements:
- Business rules are documented.
- Edge cases are covered or accepted as risk.
- Out-of-scope items are explicit.

Architecture:
- Database migrations are reviewed.
- Rollback strategy exists.
- No unapproved dependencies were added.
- Auth and authorization behavior are clear.
- Data retention expectations are known.

Implementation:
- Changed files are listed.
- Feature flags or rollout controls are documented if used.
- Known limitations are documented.

Quality:
- Relevant tests passed.
- Regression risks are known.
- Bugs are closed or accepted.
- Manual checks are documented if automation is incomplete.

Delivery:
- Deployment commands are known.
- Environment variables are documented without secret values.
- Monitoring and alerting expectations are defined.
- Post-release validation owner is assigned.
```

Example production-readiness decision:

```text
Release recommendation:
Proceed with internal beta.

Required before public release:
- Add UI-level test coverage for the checklist completion flow.
- Add dashboard alert for failed weekly run generation.
- Document CSV report columns in tracked user documentation.

Rollback:
- Revert application deployment.
- If database rollback is supported, run the generated down migration.
- If database rollback is not supported, disable checklist routes and preserve data for manual recovery.
```

## Stage 12: Deploy

Deployment is project-specific. This seed does not define a universal deployment command.

Use the team to verify the project's real deployment path:

```text
Use `team_architect`, `software_developer`, `tester`, and `scrum_master` to inspect the repository and identify the deployment path.

Output:
- Build command
- Test command
- Migration command
- Deployment command or CI/CD workflow
- Required environment variables by name only
- Rollback procedure
- Post-deploy validation checklist

Do not print secret values.
Persist evidence under `workspace/2026-05-23-production-readiness-ops-checklist-mvp/06-delivery/deployment-runbook.md`.
```

Example deployment runbook shape:

```text
Build:
- `npm run build`

Validation:
- `npm test`
- `npm run typecheck`

Migration:
- `npm run db:migrate`

Deploy:
- Use the existing CI/CD workflow `.github/workflows/deploy.yml`

Post-deploy validation:
- Log in as an operations manager.
- Create a checklist template.
- Generate a weekly run.
- Complete one checklist item.
- Export weekly CSV report.
- Confirm no new errors in logs.
```

## Stage 13: Post-Release Validation

After deployment, validate the production behavior without exposing sensitive data.

Prompt:

```text
Use `tester`, `product_owner`, and `scrum_master` for post-release validation.

Inputs:
- Production readiness evidence
- Deployment runbook
- Non-sensitive validation observations

Output:
- Acceptance validation result
- Production issues found
- User-impact assessment
- Release decision
- Follow-up backlog

Persist evidence under `workspace/2026-05-23-post-release-ops-checklist-mvp/`.
```

Post-release evidence:

```text
Acceptance validation:
- Template creation works in production.
- Weekly run generation works in production.
- Completion flow works in production.
- CSV export works in production.

Issues:
- No blocking issues found.

Follow-up backlog:
- Add Slack reminder integration.
- Add audit log for compliance exports.
- Add UI automation for completion flow.
```

## Stage 14: Promote Durable Decisions

Most `workspace/` evidence is local and untracked. Promote only durable decisions into tracked files.

Good candidates for tracked docs:

- ADRs that explain architecture decisions.
- Public API documentation.
- User-facing report format documentation.
- Deployment runbooks.
- Security or compliance decisions.
- Long-lived business rules.

Poor candidates for tracked docs:

- Temporary scratch notes.
- Raw validation logs.
- Repeated agent summaries.
- Local debugging output.
- Any secret-bearing content.

Example promotion prompt:

```text
Review `workspace/2026-05-23-ops-checklist-mvp/`.

Recommend which artifacts should be promoted into tracked project documentation.
For each recommendation, explain why it should be durable.
Do not move files until I approve.
```

## Role Dictionary

`product_owner`

- Use for product value, scope, priority, release intent, MVP boundaries, user value, and acceptance decisions.
- Do not use for low-level technical design.
- For frontend work, use `docs/ui-design.md` to keep product acceptance intent aligned with the default visual direction.
- Typical evidence: product brief, MVP scope, out-of-scope list, product risks, release recommendation.

`business_analyst`

- Use for requirements, workflows, business rules, edge cases, user stories, and acceptance criteria.
- Do not use for architecture or implementation choices.
- For frontend work, include UI states, responsive behavior, accessibility expectations, and `docs/ui-design.md` conformance in acceptance criteria.
- Typical evidence: requirements matrix, user stories, acceptance criteria, traceability matrix.

`team_architect`

- Use for architecture, data flow, dependencies, security boundaries, NFRs, ADRs, and guardrails.
- Do not use to expand product scope.
- For frontend work, define guardrails for `docs/ui-design.md`, Tailwind CSS usage, and any existing project design-system conflicts.
- Typical evidence: architecture review, target design, alternatives, risks, guardrails, ADR draft.

`software_developer`

- Use for focused implementation, tests, refactors, bug fixes, and validation commands.
- Do not use to silently change product scope or introduce dependencies without approval.
- For frontend work, implement against `docs/ui-design.md` and prefer Tailwind CSS when available without adding an unapproved dependency.
- Typical evidence: implementation plan, implementation summary, changed files, validation results.

`tester`

- Use for test strategy, acceptance validation, regression risk, bug reports, and release-quality evidence.
- Do not use to weaken acceptance criteria or ignore failed tests.
- For frontend work, validate design-guide conformance, responsive behavior, accessibility, and interaction states.
- Typical evidence: test plan, test cases, validation results, bug reports, release-quality recommendation.

`scrum_master`

- Use for coordination, blockers, owners, Definition of Done, handoffs, and next actions.
- Do not use for product priority or architecture decisions.
- For frontend work, make design-guide conformance, Tailwind constraints, responsive checks, and accessibility checks visible in the Definition of Done.
- Typical evidence: team status, work item owners, impediment log, DoD checklist, handoff summary.

## Prompt Dictionary

Start team router mode for an interactive session:

```text
Read `.codex/team-prompts/team-router-session.md` and execute it.
```

Full team discovery:

```text
Read `.codex/team-prompts/autonomous-delivery-workflow.md` and execute it.

Task:
<idea or problem>

Delivery mode:
implement

Frontend/UI:
- Use `docs/ui-design.md` if frontend pages or components are affected.
- Prefer Tailwind CSS when available without adding an unapproved dependency.

Use the project subagents under `.codex/agents/`.
Follow `AGENTS.md`.
Persist evidence under `workspace/YYYY-MM-DD-short-task-slug/`.
```

Manual discovery only:

```text
Read `.codex/team-prompts/software-team-workflow.md` and execute it.

Task:
<idea or problem>

Use the project subagents under `.codex/agents/`.
Follow `AGENTS.md`.
Do not write production code yet.
Persist evidence under `workspace/YYYY-MM-DD-short-task-slug/`.
```

Product-only refinement:

```text
Use `product_owner` to refine product value, MVP scope, out-of-scope items, priorities, and release intent.
Persist product evidence under `workspace/YYYY-MM-DD-short-task-slug/01-product/`.
Do not write code.
```

Requirements refinement:

```text
Use `business_analyst` to produce user stories, business rules, edge cases, acceptance criteria, and traceability.
Persist requirements evidence under `workspace/YYYY-MM-DD-short-task-slug/02-requirements/`.
Do not write code.
```

Architecture review:

```text
Use `team_architect` to inspect the repository and produce architecture guardrails.
Persist architecture evidence under `workspace/YYYY-MM-DD-short-task-slug/03-architecture/`.
Do not write code.
```

Implementation:

```text
Use `software_developer` to implement the accepted slice.
Follow the requirements and architecture guardrails.
Add or update tests where feasible.
Run relevant validation commands.
Persist implementation evidence under `workspace/YYYY-MM-DD-short-task-slug/04-implementation/`.
```

Testing:

```text
Use `tester` to validate the implementation against acceptance criteria.
Run available validation commands.
Report blocking bugs, coverage gaps, and release-quality recommendation.
Persist quality evidence under `workspace/YYYY-MM-DD-short-task-slug/05-quality/`.
```

PR review:

```text
Read `.codex/team-prompts/pr-review-workflow.md` and execute it.
Review the current branch against `master`.
Do not make code changes.
Persist review evidence under `workspace/YYYY-MM-DD-pr-review-short-slug/`.
```

Production readiness:

```text
Use the project subagents to prepare a production readiness review.
Focus on deployment, config, secrets, migrations, observability, security, rollback, and post-release validation.
Do not print secret values.
Persist evidence under `workspace/YYYY-MM-DD-production-readiness-short-slug/`.
```

## Artifact Dictionary

`00-task.md`

- Captures the original task, assumptions, date, owner, and workspace slug.

`01-product/product-brief.md`

- Captures product problem, users, goals, MVP, out-of-scope items, success metrics, and product risks.

`01-product/acceptance-intent.md`

- Captures product-level acceptance intent before detailed acceptance criteria.

`01-product/release-risk.md`

- Captures product risk and release recommendation.

`02-requirements/requirements.md`

- Captures functional requirements, actors, business rules, and edge cases.

`02-requirements/acceptance-criteria.md`

- Captures observable Given/When/Then or checklist-style acceptance criteria.

`02-requirements/traceability.md`

- Maps product goals to requirements, acceptance criteria, and test ideas.

`03-architecture/architecture-review.md`

- Captures current-state evidence, target design, affected components, and tradeoffs.

`03-architecture/implementation-guardrails.md`

- Captures constraints that implementation must follow.

`03-architecture/adr-draft.md`

- Captures an Architecture Decision Record draft when a durable decision is needed.

`04-implementation/implementation-plan.md`

- Captures the proposed focused implementation plan.

`04-implementation/implementation-summary.md`

- Captures what changed, files changed, key decisions, tests, validation, risks, and follow-ups.

`04-implementation/changed-files.md`

- Captures changed files with a short explanation for each.

`05-quality/test-plan.md`

- Captures test strategy, test cases, acceptance coverage, and regression areas.

`05-quality/validation-results.md`

- Captures validation commands, pass/fail status, and important observations.

`05-quality/bug-reports.md`

- Captures reproducible bugs with severity, steps, expected result, actual result, and suspected area.

`05-quality/release-quality.md`

- Captures quality recommendation and residual risk.

`06-delivery/dod-checklist.md`

- Captures Definition of Done status.

`06-delivery/handoff-summary.md`

- Captures cross-role handoff notes.

`06-delivery/production-readiness.md`

- Captures readiness for deployment and operation.

`06-delivery/next-actions.md`

- Captures owners and recommended next steps.

`07-service-manual/service-manual.md`

- Captures the synthesized service manual draft contributed by all relevant roles and merged by the parent or synthesis agent.

## Quality Gates

Use these gates before moving forward.

Before implementation:

- Product scope is accepted or explicitly assumed.
- Requirements are testable.
- Architecture guardrails are clear.
- Risks and open questions are visible.

Before PR review:

- Implementation summary exists.
- Relevant tests were run or skipped with reason.
- Known limitations are documented.
- Workspace artifact index exists.

Before merge:

- No blocking review findings remain.
- Acceptance criteria are covered or accepted as risk.
- Commit messages follow `docs/commit-message-standard.md`.

Before production:

- Deployment path is known.
- Secrets are not printed or committed.
- Database migration and rollback are understood.
- Monitoring or validation plan exists.
- Product owner accepts release risk with tester evidence.

After production:

- Post-release validation is complete.
- Production issues are triaged.
- Follow-up backlog is captured.
- Durable decisions are promoted to tracked docs when useful.

## Common Workflows

New feature:

```text
Run `.codex/team-prompts/autonomous-delivery-workflow.md` with delivery mode `implement` or `production-readiness`.
```

Bug fix:

```text
tester reproduces bug -> business_analyst confirms expected behavior if unclear -> software_developer fixes -> tester validates -> PR review if risk is non-trivial.
```

Refactor:

```text
team_architect defines guardrails -> software_developer performs focused refactor -> tester validates no behavior change -> PR review checks regression risk.
```

Production incident:

```text
tester captures reproduction and impact -> software_developer mitigates -> team_architect assesses systemic risk -> product_owner assesses user impact -> scrum_master tracks owners and follow-up.
```

PR review:

```text
Run `.codex/team-prompts/pr-review-workflow.md` against `master`, persist review evidence, resolve blocking findings, then merge.
```

## Troubleshooting

Problem: Codex did not use subagents.

Solution:

```text
Enter team router mode by reading `.codex/team-prompts/team-router-session.md` and executing it.
For this request, route work to the smallest relevant set of project subagents under `.codex/agents/`, wait for required results, and return one unified final answer.
```

Problem: Workflow prompt was ignored.

Solution:

```text
Read `.codex/team-prompts/autonomous-delivery-workflow.md` and execute it.
```

Problem: Evidence was not written to `workspace/`.

Solution:

```text
Persist evidence under `workspace/YYYY-MM-DD-short-task-slug/`.
Include an artifact index in the final response.
```

Problem: Read-only agents cannot write files.

Solution:

```text
Read-only agents should return workspace-ready Markdown evidence to the parent or synthesis agent.
The parent or synthesis agent persists the evidence without changing the role's decisions.
```

Problem: The team is over-processing a tiny task.

Solution:

```text
Use only the needed role. For example, use `software_developer` for a small code fix and `tester` for validation.
```

Problem: The workspace contains useful long-term decisions.

Solution:

```text
Promote durable decisions into tracked docs after review. Keep raw workspace evidence untracked.
```

## Final End State Of The Example

At the end of the example, the project should have:

- A scoped MVP for recurring compliance checklists.
- Testable requirements and acceptance criteria.
- Architecture guardrails and any needed ADR draft.
- Implemented MVP slices with focused diffs.
- Validation evidence for each slice.
- PR review evidence across product, requirements, architecture, implementation, quality, and delivery.
- Production readiness evidence.
- A deployment runbook.
- Post-release validation evidence.
- A follow-up backlog.

Tracked repository artifacts may include:

- Source code changes.
- Tests.
- Durable docs such as ADRs, API docs, user docs, or deployment runbooks.
- Conventional Commits-compliant commit history.

Local untracked artifacts remain under:

```text
workspace/
```

This is the intended end state: the code is delivered, the reasoning is auditable, the release risk is explicit, and the next iteration has a clean starting point.
