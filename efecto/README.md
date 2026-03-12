# Efecto Plugin for Claude Code

Design web pages with AI using [Efecto](https://efecto.app) — a real-time visual design tool that AI agents control programmatically.

## What's Included

- **MCP Server** — Full access to the Efecto design tool (44 tools for creating, modifying, and organizing designs)
- **Web Design Skill** — Comprehensive guide for building web pages and app UIs with JSX + Tailwind CSS via Efecto sessions
- **Social Media Skill** — Platform-specific templates for Instagram, YouTube, TikTok, Twitter/X, LinkedIn, Pinterest, and Facebook
- **Graphic Design Skill** — Presentations, pitch decks, event posters, email headers, blog heroes, OG images, and certificates

## Install

```bash
claude plugin add efecto
```

Or test locally:

```bash
claude --plugin-dir ./plugin
```

## Usage

Once installed, Claude automatically uses the Efecto tools when you ask it to design something:

- "Design a landing page for my startup"
- "Create an Instagram carousel about design tips"
- "Build a pricing page with dark mode"

### Skills

| Skill | Trigger | Description |
|-------|---------|-------------|
| `/efecto:web-design` | Design tasks, web pages, layouts | Full design tool reference with JSX patterns and Tailwind CSS |
| `/efecto:social-media` | Social media content | Instagram, YouTube, TikTok, Twitter/X, LinkedIn, Pinterest, Facebook |
| `/efecto:graphic-design` | Presentations, posters, graphics | Pitch decks, event posters, email headers, OG images, certificates |

## How It Works

1. Claude creates an Efecto session via MCP
2. You open the design URL in your browser
3. Claude pushes design commands — you see every change live
4. Iterate with natural language until the design is perfect

## Requirements

- [Claude Code](https://claude.ai/code) CLI
- A modern browser (for the design preview)

## Links

- [Efecto App](https://efecto.app)
- [GitHub](https://github.com/pablostanley/efecto-app)
- [MCP Package](https://www.npmjs.com/package/@efectoapp/mcp)
