# Efecto Plugin

Design with AI agents using [Efecto](https://efecto.app) — a real-time visual design tool where humans and robots design together.

Works with **Claude Code**, **Cursor**, **Codex**, and any MCP-compatible client.

## What's Included

- **MCP Server** — 46 tools for creating, modifying, and organizing designs programmatically
- **Web Design Skill** — Build web pages and app UIs with JSX + Tailwind CSS
- **Social Media Skill** — Instagram, YouTube, TikTok, Twitter/X, LinkedIn, Pinterest, Facebook
- **Graphic Design Skill** — Presentations, pitch decks, event posters, email headers, OG images

## Install

### Skills CLI (works with any agent)

```bash
npx skills add pablostanley/efecto-plugin
```

### Claude Code

```bash
claude plugin add efecto
```

### Cursor

[Install with one click](cursor://anysphere.cursor-deeplink/mcp/install?name=efecto&config=eyJjb21tYW5kIjoibnB4IiwiYXJncyI6WyIteSIsIkBlZmVjdG9hcHAvbWNwIl19) (opens Cursor automatically), or add manually to `.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "efecto": {
      "command": "npx",
      "args": ["-y", "@efectoapp/mcp"]
    }
  }
}
```

### OpenAI Codex

```bash
codex mcp add efecto -- npx -y @efectoapp/mcp
```

Or add to `~/.codex/config.toml`:

```toml
[mcp_servers.efecto]
command = "npx"
args = ["-y", "@efectoapp/mcp"]
```

### Any MCP Client

```bash
npx @efectoapp/mcp
```

## How It Works

1. Your AI agent creates an Efecto session via MCP
2. You open the design URL in your browser
3. The agent pushes design commands — you see every change live
4. Iterate with natural language until the design is perfect

## Usage

Just ask your AI to design something:

- "Design a landing page for my startup"
- "Create an Instagram carousel about design tips"
- "Build a pricing page with dark mode"
- "Make a pitch deck for our Series A"

## Links

- [Efecto App](https://efecto.app)
- [GitHub](https://github.com/pablostanley/efecto-app)
- [MCP Package](https://www.npmjs.com/package/@efectoapp/mcp)
