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
      team-router-session.md
      autonomous-delivery-workflow.md
      software-team-workflow.md
      pr-review-workflow.md
```

## Start Codex

From the repository root:

```bash
codex
```

For an interactive session where later messages should behave like requests to a software team, paste `.codex/team-prompts/team-router-session.md` once at the start. After that, describe ideas, bugs, requirements, review needs, or release questions naturally; the parent agent routes work to the smallest relevant set of agents and returns one unified answer or implementation result.

For a large end-to-end delivery request, paste `.codex/team-prompts/autonomous-delivery-workflow.md` and fill in the `Task` and `Delivery mode` fields. Use the other workflow prompts only when you want a narrower team workflow.

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

## Service manual draft

For product, feature, implementation, production-readiness, or release-support work, the team maintains a synthesized service manual draft at `workspace/YYYY-MM-DD-short-task-slug/07-service-manual/service-manual.md`.

Each agent contributes only its role-owned content. Role ownership controls who provides evidence, not how the final document is structured. The parent or synthesis agent edits the contributions into one fluent manual with a single editorial voice, consistent terminology, consistent heading style, and a service-centered reading order. Keep it as local workspace evidence until you explicitly approve promoting durable content into tracked docs such as `docs/service-manual.md`.

## Frontend design guide

When a task affects frontend pages, UI components, or user-facing interaction states, the team uses `docs/ui-design.md` as the default design system. Prefer Tailwind CSS for styling when the target project already uses Tailwind or can use it without adding an unapproved production dependency.

## Language policy

The team is English-first. Agent outputs, role artifacts, handoff notes, review findings, commit message guidance, and workflow prompts are written in English by default.

## Important usage note

This seed is designed for team router mode. The parent agent should answer simple requests directly and automatically use the smallest relevant set of subagents for non-trivial product, requirement, bug, implementation, review, release, or production-readiness work. The user-facing result should still be one coherent answer from the parent agent, not raw role transcripts.
