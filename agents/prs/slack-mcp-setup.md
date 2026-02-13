# Slack MCP Setup for Claude Code

## What works

`@modelcontextprotocol/server-slack` via stdio with a user OAuth token (`xoxp-`). Posts as you with your name and avatar.

## What does not work

- `https://mcp.slack.com/mcp` direct URL — fails with "does not support dynamic client registration"
- `mcp-remote` proxy to `https://mcp.slack.com/mcp` — same error, mcp-remote can't handle it either
- `mcp-remote` proxy to `https://mcp.slack.com/sse` — 404, endpoint doesn't exist
- `@modelcontextprotocol/server-slack-user` (lars-hagen) — not published to npm, package not found
- `@anthropic-ai/mcp-remote` — package doesn't exist on npm (correct name is just `mcp-remote`)

## Setup steps

### 1. Create a Slack app

Go to https://api.slack.com/apps and create a new app from scratch. Pick your workspace.

### 2. Add User Token Scopes

Under OAuth & Permissions, add these under **User Token Scopes** (not Bot Token Scopes):

- `channels:history`
- `channels:read`
- `chat:write`
- `groups:history`
- `groups:read`
- `reactions:write`
- `users:read`
- `users:read.email`
- `usergroups:read` (required to resolve user group mentions like `@engineers`)

### 3. Install to workspace

Click "Install to Workspace" and authorize. Copy the **User OAuth Token** (starts with `xoxp-`).

### 4. Get your team ID

Open Slack in a browser. The URL looks like `https://app.slack.com/client/TXXXXXXXX/...`. The `T...` segment is your team ID.

### 5. Add to Claude Code

```bash
claude mcp add slack \
  -e SLACK_BOT_TOKEN=xoxp-your-token \
  -e SLACK_TEAM_ID=T-your-team-id \
  -- npx -y @modelcontextprotocol/server-slack
```

The env var is named `SLACK_BOT_TOKEN` but accepts user tokens.

### 6. Restart Claude Code

Run `/mcp` to verify the server shows as connected.

## Notes

- The package shows a deprecation warning on install. It still works as of Feb 2026.
- User tokens (`xoxp-`) post as you. Bot tokens (`xoxb-`) post as the app.
- Tagging users (`<@U12345>`) and `@channel`/`@here` only requires `chat:write`.
- Mentioning user groups (e.g. `@engineers`) requires `usergroups:read` and the syntax `<!subteam^GROUP_ID|name>`. Plain text `@engineers` renders as plain text.
- Adding new scopes requires reinstalling the app to the workspace. The old token is invalidated and a new one is issued.
- If the server ends up in `disabledMcpServers` in `~/.claude.json`, manually edit the array to be empty (`[]`).
- Token is stored in `~/.claude.json` under the project-scoped config. Not encrypted.
