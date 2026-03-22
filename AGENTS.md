# Efecto — Agent Instructions

Efecto is a real-time visual design tool that AI agents control programmatically via MCP (Model Context Protocol).

## Setup

Install skills + MCP tools:

```
npx skills add pablostanley/efecto-plugin
```

This installs 3 design skills and configures the Efecto MCP server with 46 design tools.

## MCP Server

The Efecto MCP server gives you 46 tools to create and modify designs in real-time:

- **Session**: `create_session`, `wait_for_connection`, `session_status`, `close_session`
- **Reading**: `get_document`, `get_selection`, `get_node_tree`, `list_artboards`, `find_nodes`
- **Creating**: `create_artboard`, `add_section`, `add_node`
- **Modifying**: `update_node`, `update_class`, `update_artboard`, `batch_update`, `replace_section`
- **Organizing**: `move_node`, `duplicate_node`, `duplicate_artboard`, `group_nodes`, `ungroup_node`, `reorder_node`
- **Selection**: `select_nodes`, `deselect_all`, `set_visibility`, `delete_nodes`, `delete_artboard`
- **Alignment**: `align_nodes`, `distribute_nodes`
- **Fill & Export**: `set_fill`, `export_image`
- **Viewport**: `zoom_to_artboard`, `zoom_to_fit`, `set_viewport`, `move_artboard`
- **Document**: `rename_document`, `new_document`
- **History**: `undo`, `redo`
- **Theme**: `get_theme`, `set_theme`, `set_theme_mode`, `reset_theme`
- **Quality**: `audit_design`, `repair_design`

## Workflow

1. Call `create_session` to get a `designUrl`
2. Tell the user to open the URL in their browser
3. Call `create_artboard` with `className: "flex flex-col"`
4. Use `add_section` with JSX + Tailwind CSS to build layouts
5. Use `get_document` to read current state and get node IDs
6. Use `batch_update` to refine styling and content

## Skills

The 3 installed skills provide design knowledge:

- **efecto-web-design** — Layout patterns, typography, component structure for web pages
- **efecto-social-media** — Platform-specific sizing, carousel templates, bold typography rules
- **efecto-graphic-design** — Presentation layouts, poster design, business cards, infographics

## Documentation

- Full docs: https://efecto.app/docs
- All 46 tools: https://efecto.app/docs/tools
- Skills guide: https://efecto.app/docs/skills
