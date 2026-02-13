# Claude Code Custom Commands

Two commands for building and maintaining per-product-area knowledge bases that Claude Code auto-loads for context.

## What these do

**`/deep-explore`** - Initial exploration of a product area. Takes a root directory and a multi-paragraph "seed" description of the product area, then systematically crawls the code and generates two files:
- `CLAUDE.local.md` - concise ~30 line architectural summary (auto-loaded by Claude Code)
- `REFERENCE.md` - detailed knowledge base with type definitions, module hierarchy, subsystem documentation, and a file index

**`/steward`** - Incremental updates. Takes a root directory, checks for new commits since the last run (tracked via a commit hash in a meta header), gathers context from PRs (via `gh` CLI) and Linear tickets (via MCP, optional), then surgically updates only the affected sections of REFERENCE.md.

Both commands are framework-agnostic. They work on React frontends, serverless backends, data pipelines, CLI tools, or anything else.

## Installation

Copy the `.md` files into your global Claude Code commands directory:

```bash
mkdir -p ~/.claude/commands
cp deep-explore.md steward.md ~/.claude/commands/
```

They are immediately available in any repo as `/deep-explore` and `/steward`.

## Gitignore setup

Add these to the `.gitignore` of any repo where you use these commands:

```
CLAUDE.local.md
REFERENCE.md
```

`CLAUDE.local.md` is often already gitignored. These are local, generated artifacts. The commands (the generation process) are the source of truth, not the output files.

## Usage

### Initial exploration

```
/deep-explore path/to/product-area

<paste several paragraphs describing what this product area does,
its key user flows, domain entities, and subsystems>
```

The seed description guides the exploration. Be specific about what the product does and what to look for. The more context you give, the better the output.

### Incremental updates

```
/steward path/to/product-area
```

Must be on `origin/main`. If nothing changed since the last run, it exits immediately. Otherwise it analyzes new commits, pulls PR descriptions and review feedback, and updates the affected sections.

### Bespoke stewards

The generic `/steward` derives its file-to-section mapping dynamically from the existing REFERENCE.md. For product areas you work in frequently, you can create a bespoke steward with a hardcoded mapping for higher precision. Place it in the project's `.claude/commands/` directory:

```bash
mkdir -p <repo>/.claude/commands
cp steward.md <repo>/.claude/commands/steward-<product-area>.md
# edit the file: hardcode the root directory, add an explicit file-to-section mapping table
```

## Prerequisites

- Claude Code CLI
- `gh` CLI (authenticated) for PR context enrichment
- Linear MCP server (optional) for ticket context enrichment
- Git repository with squash-merge PRs (commit messages contain `(#NNNN)`)

## Commands vs agents vs skills

These are **commands** (prompt templates invoked with `/name`, run inline in your conversation). If you later want a "manager" that coordinates multiple stewards updating different product areas in parallel, convert the stewards to **agents** (`.claude/agents/*.md`) so they can be launched as concurrent subprocesses via the Task tool.
