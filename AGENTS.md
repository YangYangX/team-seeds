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

- Use subagents only when the user explicitly asks for a team/subagent workflow, or when the user asks to use one of the named roles.
- Use `single-writer-multiple-readers` for implementation work:
  - `software_developer` is the default writer for production code.
  - `tester` may write or modify test files when useful.
  - `product_owner`, `business_analyst`, `team_architect`, and `scrum_master` are read-only by default.
- Wait for required subagent outputs before producing the consolidated team answer.
- Do not let each agent solve the entire task. Each role should stay within its responsibility boundary.

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
- Each evidence file must include `Status`, `Owner`, `Created`, `Source task`, and a concise evidence summary.
- Agents with write access may write only their own evidence files. Read-only agents must return evidence to the parent or synthesis agent, which persists it without changing the role's decisions.
- Never store secrets, tokens, credentials, private keys, `.env` contents, customer-sensitive data, or production-only configuration values in `workspace/`.
- The final consolidated team answer must include an artifact index with file paths, owners, and status.

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
