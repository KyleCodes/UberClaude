# UberClaude

Reusable Claude Code commands and agents for common development workflows.

These are framework-agnostic and can be dropped into any codebase. They encode opinionated processes for knowledge base management and PR workflows, but the tooling itself is generic.

## What's here

### [commands/knowledge/](./commands/knowledge/)

Commands for building and maintaining per-product-area knowledge bases that Claude Code auto-loads for context.

- **`/deep-explore`** - Comprehensive initial exploration of a product area. Takes a root directory and a seed description, crawls the code, and generates a concise `CLAUDE.local.md` + detailed `REFERENCE.md`.
- **`/steward`** - Incremental updates. Checks for new commits since the last run, gathers PR and issue tracker context, and surgically updates only the affected sections.

### [agents/prs/](./agents/prs/)

Agent for PR creation and Slack review requests.

- **`pr-workflow`** - Reads the diff, writes a concise PR description (product impact first, technical details second), and drafts Slack messages with clickable hyperlinks. Always asks for approval before posting.

## Installation

```bash
# knowledge base commands
mkdir -p ~/.claude/commands
cp commands/knowledge/deep-explore.md commands/knowledge/steward.md ~/.claude/commands/

# pr workflow agent
mkdir -p ~/.claude/agents
cp agents/prs/pr-workflow.md ~/.claude/agents/
```

## Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI
- `gh` CLI (authenticated)
- Git
- Slack MCP server (optional, see [agents/prs/slack-mcp-setup.md](./agents/prs/slack-mcp-setup.md))
- Issue tracker MCP (optional, for steward ticket context)
