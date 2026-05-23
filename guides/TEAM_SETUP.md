# Codex Software Team Agents

This package defines an English-first Codex subagent team for software development.

Project-scoped custom agents:

- `product_owner`
- `business_analyst`
- `team_architect`
- `software_developer`
- `tester`
- `scrum_master`

## Install into a repository

Copy the `.codex/` directory, `AGENTS.md`, `.gitignore`, `workspace/.gitignore`, and runtime docs such as `docs/commit-message-standard.md` and `docs/ui-design.md` into the root of your repository.

Optionally copy `guides/` if you want the human setup and end-to-end usage manuals in the target repository.

If the target repository already has a root `.gitignore`, `docs/`, or `guides/` directory, merge the files from this package instead of replacing project-specific files.

Directory intent:

- `.codex/`: Codex configuration, custom agents, and workflow prompts.
- `docs/`: Runtime-readable team standards and project guidance that agents should use during work, such as commit and UI design standards.
- `guides/`: Human-facing setup and usage manuals; agents should not treat these as runtime requirements.
- `workspace/`: Local, untracked team evidence.

Recommended layout:

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
      autonomous-delivery-workflow.md
      software-team-workflow.md
      pr-review-workflow.md
```

## Start Codex

From the repository root:

```bash
codex
```

For most work, paste `.codex/team-prompts/autonomous-delivery-workflow.md` and fill in the `Task` and `Delivery mode` fields. Use the other workflow prompts only when you want a narrower team workflow.

For a full idea-to-production example, see `guides/END_TO_END_USAGE_EXAMPLE.md`.

## Permission defaults

This seed sets these Codex defaults in `.codex/config.toml`:

```toml
approval_policy = "never"
sandbox_mode = "workspace-write"
```

This prevents routine shell commands from blocking on repeated CLI approval prompts while keeping command execution inside the workspace sandbox. Team rules still require explicit user approval before destructive operations, production deployment, new production dependencies, public compatibility changes, production configuration changes, or secret handling.

If you want Codex to ask before selected commands during an interactive session, change `approval_policy` to `on-request`.

## Workspace evidence

Team workflows persist role evidence as Markdown under `workspace/YYYY-MM-DD-short-task-slug/`.

The `workspace/` directory is intentionally untracked except for `workspace/.gitignore`. It is for local agent evidence, validation notes, handoffs, and release-readiness records. Promote durable decisions into tracked project docs only when they should become part of the repository.

## Frontend design guide

When a task affects frontend pages, UI components, or user-facing interaction states, the team uses `docs/ui-design.md` as the default design system. Prefer Tailwind CSS for styling when the target project already uses Tailwind or can use it without adding an unapproved production dependency.

## Language policy

The team is English-first. Agent outputs, role artifacts, handoff notes, review findings, commit message guidance, and workflow prompts are written in English by default.

## Important usage note

Codex custom agents are available as subagents, but Codex only spawns subagents when explicitly asked. Use prompts that name the target agents and ask Codex to spawn them, wait for their results, and synthesize the final answer.
