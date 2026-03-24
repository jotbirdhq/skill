# JotBird Skill

An [agent skill](https://agentskills.io) that lets AI agents publish Markdown as shareable web pages on [JotBird](https://www.jotbird.com).

## What it does

- **Publish** Markdown as a beautifully formatted web page with a shareable URL
- **Update** existing pages in place
- **List** all published documents
- **Delete** published pages

## Install

```bash
npx skills add jotbirdhq/skill
```

## Prerequisites

You need a free JotBird account. Sign up at [jotbird.com](https://www.jotbird.com), then run:

```bash
npx jotbird login
```

## Example

Ask your AI agent:

> "Write a summary of today's meeting and publish it as a web page."

The agent writes the Markdown, publishes it via the JotBird CLI, and gives you a shareable link.

## Links

- [JotBird](https://www.jotbird.com)
- [CLI documentation](https://www.jotbird.com/cli)
- [MCP server](https://www.jotbird.com/mcp)
- [Agent Skills format](https://agentskills.io)
