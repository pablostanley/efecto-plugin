# Efecto — AI Design Skills + MCP Tools

Design with AI agents using [Efecto](https://efecto.app) — a real-time visual design tool where humans and robots design together.

Works with **Claude Code**, **Cursor**, **Windsurf**, **GitHub Copilot**, **Codex**, **Gemini CLI**, and any MCP-compatible agent.

## Install

```bash
npx skills add pablostanley/efecto-plugin
```

This installs **3 design skills** (knowledge) + **46 MCP tools** (actions). Your AI agent gets both the taste and the tools.

<details>
<summary>Other install methods</summary>

### Claude Code Plugin

```bash
claude plugin add efecto
```

### Cursor

[Install with one click](cursor://anysphere.cursor-deeplink/mcp/install?name=efecto&config=eyJjb21tYW5kIjoibnB4IiwiYXJncyI6WyIteSIsIkBlZmVjdG9hcHAvbWNwIl19) or add to `.cursor/mcp.json`:

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

### Windsurf / Copilot / Gemini / Any MCP Client

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

</details>

## What's Included

### 46 MCP Tools

Create sessions, build artboards, add layouts with JSX + Tailwind CSS, modify nodes, export images, manage themes, and more. Full programmatic control of the Efecto design canvas.

### 3 Design Skills

Skills teach your AI agent *how to design well* — not just how to call tools. Without skills, your agent can create artboards and add sections. With skills, it knows typography scales, platform-specific sizing, layout patterns, and visual hierarchy.

| Skill | What it teaches |
|-------|----------------|
| **Web Design** | Landing pages, dashboards, marketing sites, pricing pages, app UIs. Layout patterns, component structure, responsive design, Tailwind best practices. |
| **Social Media** | Instagram carousels, YouTube thumbnails, TikTok covers, Twitter/X images, LinkedIn slides, Pinterest pins. Platform-specific sizing, bold typography, scroll-stopping design. |
| **Graphic Design** | Pitch decks, event posters, business cards, resumes, menus, infographics, invitations, newsletters, email headers, OG images, certificates, documents. |

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
- "Design a business card for my agency"
- "Create a YouTube thumbnail for my video"

## Links

- [Efecto App](https://efecto.app)
- [Documentation](https://efecto.app/docs)
- [Skills Guide](https://efecto.app/docs/skills)
- [All 46 Tools](https://efecto.app/docs/tools)
- [MCP Package](https://www.npmjs.com/package/@efectoapp/mcp)
