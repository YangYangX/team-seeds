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

Copy the `.codex/` directory, `AGENTS.md`, `commit-message-standard.md`, `.gitignore`, and `workspace/.gitignore` into the root of your repository.

If the target repository already has a root `.gitignore`, merge the patterns from this package instead of replacing project-specific ignore rules.

Recommended layout:

```text
<repo>/
  .gitignore
  AGENTS.md
  commit-message-standard.md
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
      software-team-workflow.md
      pr-review-workflow.md
```

## Start Codex

From the repository root:

```bash
codex
```

Then paste one of the workflow prompts from `.codex/team-prompts/`.

## Workspace evidence

Team workflows persist role evidence as Markdown under `workspace/YYYY-MM-DD-short-task-slug/`.

The `workspace/` directory is intentionally untracked except for `workspace/.gitignore`. It is for local agent evidence, validation notes, handoffs, and release-readiness records. Promote durable decisions into tracked project docs only when they should become part of the repository.

## Language policy

The team is English-first. Agent outputs, role artifacts, handoff notes, review findings, commit message guidance, and workflow prompts are written in English by default.

## Important usage note

Codex custom agents are available as subagents, but Codex only spawns subagents when explicitly asked. Use prompts that name the target agents and ask Codex to spawn them, wait for their results, and synthesize the final answer.
