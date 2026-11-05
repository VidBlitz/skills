# Vidblitz for AI agents

[Vidblitz](https://vidblitz.ai) makes faceless YouTube videos from a topic or your own script: researched, narrated, sourced, edited and rendered, with thumbnails, title ideas, a description and chapters.

This repository teaches AI agents to use it. Ask in plain words, for example *"make a 10-minute documentary about the Apollo 11 landing"* or *"write a 3-minute script about octopus intelligence with me, then make it a video"*. The agent creates the video, tells you exactly how many credits it costs, and starts production only after you say yes.

## Claude Code

```
/plugin marketplace add vidblitz/skills
/plugin install vidblitz@vidblitz
```

The plugin adds the Vidblitz skill and connects the Vidblitz MCP server. Run `/mcp`, choose **vidblitz**, then **Authenticate** to sign in with your Vidblitz account.

## Codex, Cursor, opencode and other agents

Install the skill with the [skills](https://github.com/vercel-labs/skills) installer:

```
npx skills add vidblitz/skills
```

You can also copy `skills/vidblitz` into your agent's skills folder. Connect the MCP server in the same agent so the skill has its tools.

## MCP only

[![Add to Cursor](https://cursor.com/deeplink/mcp-install-dark.svg)](https://cursor.com/install-mcp?name=vidblitz&config=eyJ1cmwiOiJodHRwczovL3ZpZGJsaXR6LmFpL21jcCJ9) [![Install in VS Code](https://img.shields.io/badge/VS_Code-Install_Vidblitz-0098FF?logo=visualstudiocode&logoColor=white)](https://vscode.dev/redirect/mcp/install?name=vidblitz&config=%7B%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fvidblitz.ai%2Fmcp%22%7D)

Point any MCP client at `https://vidblitz.ai/mcp` (Streamable HTTP, OAuth sign-in). Setup for ChatGPT, Claude, Cursor and VS Code is at [vidblitz.ai/developers/mcp](https://vidblitz.ai/developers/mcp).

## What's inside

| Path | What it is |
|---|---|
| `skills/vidblitz/SKILL.md` | How to make a video from a topic or a script, and the rule to quote before spending credits |
| `skills/vidblitz/references/rest.md` | The REST calls behind each tool, for agents writing code |
| `.mcp.json` | The Vidblitz MCP server, for the Claude Code plugin |
| `.claude-plugin/` | The Claude Code plugin and marketplace manifests |
| `.codex-plugin/` | The plugin manifest for ChatGPT and Codex, with its listing details and review test cases |
| `assets/` | The icon and logo the plugin listings use |

## Links

- [Developer docs](https://vidblitz.ai/developers): MCP server, REST API, authentication
- [Full reference for LLMs](https://vidblitz.ai/developers/llms.txt)
- Support: support@vidblitz.ai

MIT licensed.
