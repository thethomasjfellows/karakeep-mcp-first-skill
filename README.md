# Karakeep MCP-First Skill

An unofficial MCP-first skill for using [Karakeep](https://karakeep.app) with AI agents.

This skill tells the agent to prefer Karakeep's official MCP server for normal bookmark/list/tag/content operations, while keeping the Karakeep CLI and direct API access as narrow fallbacks for unsupported actions, bulk work, debugging, raw verification, and highlight creation.

It was created for the YouTube video: [I Gave My AI Agent Access to 400+ Karakeep Bookmarks. Then This Happened](https://youtu.be/WQNhRyYyl64).

## Why This Exists

Karakeep already has agent tooling, but this version is intentionally MCP-first:

- Use MCP for normal agent work.
- Use CLI only when MCP is unavailable, incomplete, or better suited for bulk/debug/raw JSON tasks.
- Use the API directly only when MCP and CLI do not expose the needed operation.
- Treat highlights as rare, meaningful annotations rather than automatic noise.

That makes the skill a better fit for agent workflows where MCP is the most structured, AI-native interface.

## Install

Copy this folder into your skills directory:

```bash
cp -R karakeep-mcp-first-skill ~/.agents/skills/karakeep-mcp-first
```

For Codex-style skills, you can also copy it to:

```bash
cp -R karakeep-mcp-first-skill ~/.codex/skills/karakeep-mcp-first
```

Then restart your agent/session so it reloads available skills.

## Requirements

- A running Karakeep instance
- A Karakeep API key
- Karakeep MCP server configured in your agent
- Optional fallback CLI:

```bash
npm install -g @karakeep/cli
```

Environment variables commonly used by Karakeep tools:

```bash
export KARAKEEP_API_KEY="your-api-key"
export KARAKEEP_SERVER_ADDR="https://your-karakeep.example.com"
```

Avoid committing real API keys or local server details into this repo or any public skill.

## Related Links

- Video walkthrough: https://youtu.be/WQNhRyYyl64
- Karakeep: https://karakeep.app
- Karakeep docs: https://docs.karakeep.app
- Karakeep GitHub: https://github.com/karakeep-app/karakeep
- Karakeep MCP package: https://www.npmjs.com/package/@karakeep/mcp

## Status

This is an unofficial community skill, not an official Karakeep project.

It is based on a real MCP-first setup used in the video above. You should adapt the organization rules, tag preferences, and fallback behavior to your own bookmark workflow.
