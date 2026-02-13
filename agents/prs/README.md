# PR Workflow Agent

Agent for creating pull requests and drafting Slack review request messages.

## Installation

```bash
mkdir -p ~/.claude/agents
cp pr-workflow.md ~/.claude/agents/
```

The agent is auto-selected when you ask Claude Code to create a PR, write a PR description, or draft a Slack message for review.

## What it does

1. Reads the full diff against the base branch (auto-detects `main` or `staging`)
2. Writes a PR title using conventional commit format: `[feat]`, `[fix]`, `[chore]`, `[refactor]`
3. Writes a concise PR description: product impact first, technical details second, concrete examples when helpful
4. Creates the PR via `gh pr create`
5. Optionally drafts a Slack message for requesting reviews

All drafts are presented for explicit approval before posting or creating.

## Style

- No emojis, no em dashes, no listicles
- Concise and dry. Two to three sentences max for Slack messages.
- Blameless: never reference the PR, commit, or person that caused an issue being fixed
- Slack messages always start with "PR to..." or "PRs to..."
- PR titles in Slack are always clickable hyperlinks using `<URL|text>` syntax

## Slack integration

If you want the agent to post review requests to Slack, you need the Slack MCP server. See [slack-mcp-setup.md](./slack-mcp-setup.md) for setup instructions.

To configure for your team, add `mcpServers: slack` to the agent frontmatter in `pr-workflow.md` and set your default channel and user group IDs. The agent will ask for these if not configured.

## Customization

The PR description template, Slack message format, and style rules are all in `pr-workflow.md`. Edit to match your team's conventions. The examples section is particularly useful for teaching the agent your preferred tone and level of detail.
