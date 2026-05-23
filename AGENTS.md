# Codex Team Operating Rules

This repository uses a Codex subagent team with these project-scoped custom agents:

- `product_owner`
- `business_analyst`
- `team_architect`
- `software_developer`
- `tester`
- `scrum_master`

## Language and communication

- Use English for all team-agent summaries, plans, handoffs, review findings, status reports, and user-facing role outputs by default.
- Keep role names, artifact names, command names, file paths, symbols, and Conventional Commits tokens in English.
- If the user explicitly asks for another language, the final user-facing explanation may use that language, but team artifacts should remain English unless the user explicitly asks to translate the artifacts too.
- Separate facts, assumptions, recommendations, and open questions.
- Use file paths, symbols, command names, and test results when discussing code behavior.
- Do not expose private chain-of-thought. Summarize rationale and evidence instead.

## Team workflow rules

- Default team router mode is enabled for this project. Treat subsequent user messages in the same session as requests to a software team unless the user explicitly asks for direct-only assistance.
- For non-trivial product, requirement, bug, implementation, review, release, or production-readiness requests, the parent agent should route work to the smallest relevant set of subagents automatically. The user should not need to paste workflow prompts repeatedly after the session is operating in team mode.
- For trivial questions, direct shell-command requests, small clarifications, or narrow documentation edits, answer directly unless a role agent is clearly useful.
- Do not spawn every agent by default. Choose roles by task type, risk, and expected evidence:
  - Product ideas, scope, priorities, or acceptance intent: `product_owner`, often with `business_analyst`.
  - Requirements, workflows, business rules, edge cases, or ambiguity: `business_analyst`, with `product_owner` for scope decisions.
  - Architecture, integrations, public APIs, data flows, security, performance, or broad technical tradeoffs: `team_architect`.
  - Code changes, refactors, bug fixes, or implementation planning: `software_developer`, with `tester` for validation.
  - Bug reports: `business_analyst` for expected behavior, `software_developer` for diagnosis/fix, `tester` for reproduction and regression evidence, and `team_architect` when the impact is cross-cutting.
  - PR/code review: use the review workflow roles needed for the changed surface.
  - Release, production-readiness, deployment planning, or operational risk: `tester`, `scrum_master`, `team_architect`, and `product_owner` when acceptance or release risk is involved.
- The parent agent remains the user-facing coordinator. Final answers should be unified and attributed to the responsible role only when helpful; do not expose raw subagent transcripts.
- Use `single-writer-multiple-readers` for implementation work:
  - `software_developer` is the default writer for production code.
  - `tester` may write or modify test files when useful.
  - `product_owner`, `business_analyst`, `team_architect`, and `scrum_master` are read-only by default.
- Wait for required subagent outputs before producing the consolidated team answer.
- Do not let each agent solve the entire task. Each role should stay within its responsibility boundary.

## Codex permission defaults

- This seed sets `approval_policy = "never"` and `sandbox_mode = "workspace-write"` in `.codex/config.toml` so routine read, write, build, lint, and test commands do not block on repeated CLI permission prompts.
- The absence of CLI permission prompts is not approval for risky work. Agents must still ask the user before destructive filesystem/database/deployment/git operations, new production dependencies, public compatibility changes, production configuration changes, or secret handling.
- If a command fails because it needs access outside the workspace or outside the sandbox, report the blocker and the smallest required user action instead of bypassing safety.

## Runtime documentation boundaries

- Treat `docs/` as runtime-readable team standards and project guidance.
- Treat `guides/` as human-facing documentation for the repository owner. Do not use files under `guides/` as task requirements, product requirements, architecture guidance, implementation guidance, testing guidance, or design guidance.
- Read or modify `guides/` only when the user explicitly asks for setup-guide, usage-guide, tutorial, or documentation work.
- If runtime guidance and `guides/` conflict, follow runtime guidance from `AGENTS.md`, `.codex/`, and `docs/`.

## Workspace evidence rules

- Use the root `workspace/` directory for team evidence artifacts created by agent workflows.
- `workspace/` is intentionally untracked except for `workspace/.gitignore`; do not rely on it as a permanent source repository artifact unless files are deliberately promoted into tracked docs.
- For each team workflow, create a task folder named `workspace/YYYY-MM-DD-short-task-slug/`.
- Persist agent evidence as Markdown files under role-oriented subdirectories when applicable:
  - `00-task.md`
  - `01-product/`
  - `02-requirements/`
  - `03-architecture/`
  - `04-implementation/`
  - `05-quality/`
  - `06-delivery/`
  - `07-service-manual/`
- Each evidence file must include `Status`, `Owner`, `Created`, `Source task`, and a concise evidence summary.
- Agents with write access may write only their own evidence files. Read-only agents must return evidence to the parent or synthesis agent, which persists it without changing the role's decisions.
- Never store secrets, tokens, credentials, private keys, `.env` contents, customer-sensitive data, or production-only configuration values in `workspace/`.
- The final consolidated team answer must include an artifact index with file paths, owners, and status.

## Service manual rules

- For product, feature, implementation, production-readiness, or release-support workflows, maintain a service manual draft at `workspace/YYYY-MM-DD-short-task-slug/07-service-manual/service-manual.md`.
- The service manual is a synthesized document for the whole service, not a raw transcript or a stitched set of role reports. Each agent contributes only the content owned by its role, then the parent or synthesis agent edits everything into one coherent Markdown manual.
- Role ownership governs source responsibility, not final document structure. Organize the final manual around how the service works, how users use it, how developers change it, how operators run it, and how the release is validated.
- The final manual must use one editorial voice, consistent terminology, consistent heading style, and a smooth reading order. Remove duplicated content, role-status chatter, handoff noise, and contradictions; unresolved contradictions must become explicit open decisions with owners.
- Default section ownership:
  - `product_owner`: service purpose, target users, product scope, value proposition, release intent, and product risks.
  - `business_analyst`: user workflows, business rules, glossary, edge cases, and acceptance behavior.
  - `team_architect`: system overview, architecture, components, integrations, data flows, non-functional requirements, and operational constraints.
  - `software_developer`: implementation notes, important modules/files, APIs/commands/configuration by name only, local development, and known technical limitations.
  - `tester`: validation strategy, test evidence summary, quality risks, known defects, troubleshooting signals, and release-quality recommendation.
  - `scrum_master`: delivery status, owners, handoffs, Definition of Done, release checklist, open decisions, and next actions.
- The service manual must avoid secrets, credentials, private keys, `.env` contents, customer-sensitive data, production-only values, and invented facts.
- Keep the workspace service manual as a draft until the user explicitly approves promoting durable content into tracked project documentation such as `docs/service-manual.md`.
- When promoting content, preserve facts, assumptions, decisions, owners, validation evidence, and known limitations.

## Frontend design rules

- When a task affects frontend pages, UI components, visual design, or user-facing interaction states, use `docs/ui-design.md` as the default design system and visual style guide.
- Prefer Tailwind CSS for frontend styling when the target project already uses Tailwind or can use it without adding an unapproved production dependency.
- If the target project has an existing design system, reconcile it with `docs/ui-design.md` and state any conflicts or necessary deviations before implementation.
- Frontend implementation should preserve the design guide's restrained editorial layout, typography, color palette, component treatments, responsive behavior, and accessibility expectations.
- Design deviations must be documented in workspace evidence with the owner, reason, and affected files or components.

## Commit message rules

- When creating or recommending commits, use the Conventional Commits standard in `docs/commit-message-standard.md`.
- If a change contains multiple unrelated intentions, recommend splitting it before committing.

## Definition of Done

A work item is done only when:

1. Acceptance criteria are clear or explicitly assumed.
2. The implementation summary lists changed files.
3. Relevant validation commands were run, or a clear reason is given for skipped validation.
4. Known risks, limitations, and follow-up items are listed.
5. Tester evidence supports the quality/release recommendation when validation is requested.

## Safety rules

- Never print, modify, or commit secrets, tokens, API keys, `.env` files, private keys, or credentials.
- Do not introduce new production dependencies without explicit approval and a reason.
- Do not run destructive database, filesystem, deployment, or git commands without explicit approval.
- Keep changes focused. Avoid unrelated refactors.

# Behavioral guidelines to reduce common LLM coding mistakes. Merge with project-specific instructions as needed.

**Tradeoff:** These guidelines bias toward caution over speed. For trivial tasks, use judgment.

## 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them - don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

## 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

## 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it - don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: Every changed line should trace directly to the user's request.

## 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.

---

**These guidelines are working if:** fewer unnecessary changes in diffs, fewer rewrites due to overcomplication, and clarifying questions come before implementation rather than after mistakes.
