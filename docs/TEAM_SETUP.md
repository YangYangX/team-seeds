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

Copy the `.codex/` directory, `AGENTS.md`, and `commit-message-standard.md` into the root of your repository.

Recommended layout:

```text
<repo>/
  AGENTS.md
  commit-message-standard.md
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

## Language policy

The team is English-first. Agent outputs, role artifacts, handoff notes, review findings, commit message guidance, and workflow prompts are written in English by default.

## Important usage note

Codex custom agents are available as subagents, but Codex only spawns subagents when explicitly asked. Use prompts that name the target agents and ask Codex to spawn them, wait for their results, and synthesize the final answer.
