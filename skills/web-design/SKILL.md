---
name: efecto-web-design
description: Design web pages and app UIs with Efecto — create sessions, build layouts with JSX and Tailwind CSS, manage artboards, and push real-time design changes via MCP tools. Use when asked to "design a page", "build a landing page", "create a website", "design a dashboard", "make a UI", or any visual design task. Requires Efecto MCP server.
metadata:
  mcp-server: efecto
  author: pablostanley
  version: "1.0.0"
  argument-hint: <design-description>
---

# Efecto — Design Web Pages with AI

Efecto is a real-time web design tool that AI agents control programmatically. You create a **session**, the user opens a URL in their browser, and you push design commands that render instantly. Every node maps to a real HTML element styled with Tailwind CSS — web-native, semantic, and pixel-accurate.

---

## Setup

This skill requires the **Efecto MCP server**. Check if it's available by looking for tools like `create_session`, `add_section`, or `get_document` in your tool list.

**If Efecto tools are NOT available, install the MCP server:**

### Claude Code
```bash
claude mcp add efecto -- npx -y @efectoapp/mcp
```

### Cursor
Add to `.cursor/mcp.json`:
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

### Windsurf / VS Code / Other MCP Clients
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

Once installed, you'll have access to 46 design tools plus a standalone image search tool. The MCP server connects your agent to the Efecto design canvas at [efecto.app](https://efecto.app).

---

## How Sessions Work

```
Agent ──POST /execute──> Server ──SSE──> Browser (renders live)
Agent <──poll/response── Server <──POST── Browser (returns results)
```

1. You create a session via the MCP server or REST API
2. The user opens the returned Efecto URL in their browser
3. You push tool calls — each one executes in the browser and returns a result
4. The user sees every change live as you build the design

Sessions expire after 15 minutes of inactivity. The browser must be connected before tool calls can execute.

---

## Quick Start — Build a Page in 7 Steps

### Step 1: Create a Session
```
create_session
  label: "Landing page design"
```
Returns `{ sessionId, designUrl }`. Tell the user to open the URL. The session ID is stored automatically — all subsequent tools use it.

### Step 2: Wait for Browser Connection
```
session_status
```
Poll until `browserConnected: true`. The browser must be open before you can push tools.

### Step 3: Create an Artboard
```
create_artboard
  name: "Homepage"
  width: 1440
  height: 900
  backgroundColor: "#ffffff"
  className: "flex flex-col"
```
Returns the artboard ID. **Always set className to "flex flex-col"** so children stack vertically.

### Step 4: Add Sections with JSX
```
add_section
  parentId: "<artboard-id>"
  jsx: '<nav className="flex items-center justify-between px-16 py-5 bg-white w-full">
    <h2 className="text-xl font-bold text-gray-900">Acme</h2>
    <div className="flex items-center gap-8">
      <a className="text-sm text-gray-600" href="#">Features</a>
      <a className="text-sm text-gray-600" href="#">Pricing</a>
      <button className="px-4 py-2 text-sm font-medium text-white bg-gray-900 rounded-lg">Get Started</button>
    </div>
  </nav>'
```
This is the most efficient way to build complex layouts — one JSX string per section.

### Step 5: Read Current State
```
get_document
```
Returns JSX-like markup with `data-id` attributes on every node. Use these IDs for updates.

### Step 6: Fine-Tune with Updates
```
batch_update
  updates: [
    { nodeId: "abc", className: "text-2xl font-extrabold text-gray-900" },
    { nodeId: "def", textContent: "Updated heading text" }
  ]
```

### Step 7: Iterate
Repeat Steps 4-6 to add more sections, adjust styling, and refine the design. The user sees every change in real time.

---

## The 46 Design Tools

### Reading State

| Tool | Purpose |
|------|---------|
| `get_selection` | Returns currently selected nodes/artboards with JSX subtrees |
| `get_document` | Returns full document as JSX with data-id attributes |
| `list_artboards` | Lists all artboards with IDs, names, dimensions |
| `find_nodes` | Search nodes by name, text content, type, or className |

### Creating Content

| Tool | Purpose |
|------|---------|
| `create_artboard` | Creates a new artboard (screen/frame) |
| `add_section` | Adds complex layouts from JSX markup (preferred) |
| `add_node` | Adds a single node to a parent |

### Modifying Content

| Tool | Purpose |
|------|---------|
| `update_node` | Updates any node property (className, textContent, tag, style, src, link, elementId, etc.) |
| `update_class` | Shortcut: replaces only className on a node |
| `update_artboard` | Updates artboard properties (name, size, background, className) |
| `batch_update` | Updates multiple nodes in one call (bulk styling) |
| `replace_section` | Replaces a node and its children with new JSX |

### Organizing Structure

| Tool | Purpose |
|------|---------|
| `move_node` | Reparents or reorders a node |
| `duplicate_node` | Deep-clones a node with fresh IDs |
| `duplicate_artboard` | Deep-clones an artboard for variations |
| `group_nodes` | Wraps selected nodes in a frame container |
| `ungroup_node` | Unwraps a group, moving children to parent |
| `reorder_node` | Brings to front or sends to back (z-order) |

### Display & Selection

| Tool | Purpose |
|------|---------|
| `select_nodes` | Highlights nodes for the user to see |
| `set_visibility` | Shows/hides nodes (like Figma eye icon) |
| `delete_nodes` | Deletes nodes and their children |
| `delete_artboard` | Deletes an artboard and all contents |

### Alignment & Distribution

| Tool | Purpose |
|------|---------|
| `align_nodes` | Aligns multiple nodes (left/center/right/top/middle/bottom) |
| `distribute_nodes` | Distributes nodes evenly (horizontal/vertical) |

### History

| Tool | Purpose |
|------|---------|
| `undo` | Undoes the last action (Cmd+Z) |
| `redo` | Redoes the last undone action (Cmd+Shift+Z) |

### Viewport & Document

| Tool | Purpose |
|------|---------|
| `zoom_to_artboard` | Zooms viewport to show a specific artboard |
| `zoom_to_fit` | Zooms to fit all artboards in the viewport |
| `set_viewport` | Sets viewport zoom level and/or pan position |
| `rename_document` | Renames the document |
| `new_document` | Creates a new blank document (replaces current) |

### Canvas & Reading

| Tool | Purpose |
|------|---------|
| `move_artboard` | Repositions an artboard on the canvas for multi-screen flows |
| `deselect_all` | Clears the current node selection |
| `get_node_tree` | Returns JSX for one node/artboard subtree (faster than full document) |

### Fill & Export

| Tool | Purpose |
|------|---------|
| `set_fill` | Sets the fill (background) of a node or artboard — solid color, gradient, or image. Handles dual-write automatically. |
| `export_image` | Exports an artboard or node as an image (PNG, JPEG, WebP, SVG). Returns base64 data URL. |

### Theme

| Tool | Purpose |
|------|---------|
| `get_theme` | Returns the current theme tokens (colors, fonts, radii) and active mode |
| `set_theme` | Sets theme tokens — colors, fonts, border radii. Accepts partial updates. |
| `set_theme_mode` | Switches between light/dark/custom modes |
| `reset_theme` | Resets theme to defaults |

### Design Validation

| Tool | Purpose |
|------|---------|
| `audit_design` | Audits design against professional quality rules: typography (scale, weight contrast, sizing), color (neutral consistency, pure black, low-contrast combos), spacing (4pt grid, touch targets), and AI slop detection (monotonous layouts). Pass `artboardId` for one artboard or omit for all. |
| `repair_design` | Applies safe, deterministic fixes across the document (or one artboard) in one pass: missing w-full, missing flex, arbitrary Tailwind values → inline styles, pure black → zinc-950, touch-target bumps, placeholder images, empty text defaults. Pass `dryRun: true` to preview. |

### Image Search

| Tool | Purpose |
|------|---------|
| `search_images` | Search for free stock images (Lummi). No session required. Returns URLs to use with `add_node` or `set_fill`. |

---

## Images — Search and Use Real Photos

Efecto includes a built-in image search powered by Lummi — a library of free, high-quality stock photos, illustrations, and 3D renders. **Use this instead of placeholder images** to make designs look polished and real.

### The `search_images` Tool

This tool works **without a session** — call it anytime, even before creating a session.

```
search_images
  query: "mountain landscape"
  orientation: "horizontal"
  limit: 4
```

Returns a list of images with `url`, `thumbnail`, `alt`, `width`, `height`, `photographer`, and `orientation`.

### Filters

| Filter | Values | When to Use |
|--------|--------|-------------|
| `query` | Any search term | Always — describe what you need ("team working", "abstract gradient", "coffee shop") |
| `orientation` | `horizontal`, `vertical`, `square` | Match the layout: horizontal for hero banners, vertical for mobile/stories, square for cards |
| `type` | `photo`, `illustration`, `3d` | Default returns all. Use "photo" for realistic imagery, "illustration" for stylized graphics |
| `luminance` | `dark`, `neutral`, `bright` | Match the design mood: "dark" for dark-themed pages, "bright" for light/airy designs |
| `limit` | 1–20 (default: 6) | Keep low (3–6) to avoid overwhelming context |

### Workflow: Search → Apply

**Step 1: Search for images**
```
search_images
  query: "modern office workspace"
  orientation: "horizontal"
  luminance: "bright"
  limit: 3
```

**Step 2a: Add as an image node**
```
add_node
  parentId: "<section-id>"
  type: "image"
  src: "<url-from-search>"
  alt: "Modern office workspace"
  className: "w-full h-[400px] rounded-2xl object-cover"
```

**Step 2b: Set as background fill**
```
set_fill
  targetId: "<section-id>"
  fill: { type: "image", url: "<url-from-search>", size: "cover", position: "center" }
```

**Step 2c: Use in JSX (add_section)**
```
add_section
  parentId: "<artboard-id>"
  jsx: '<section className="flex items-center gap-16 px-16 py-24 w-full">
    <div className="flex flex-col gap-6 grow">
      <h1 className="text-5xl font-bold text-gray-900">Our Story</h1>
      <p className="text-lg text-gray-500">Building the future of design.</p>
    </div>
    <img src="<url-from-search>" alt="Team at work" className="w-[560px] h-[380px] rounded-2xl object-cover" />
  </section>'
```

### Tips

- **Search before designing.** Find images first, then build the layout around them. This produces better compositions than adding images as afterthoughts.
- **Match orientation to layout.** Horizontal images for hero sections, vertical for sidebar/mobile, square for grid cards.
- **Use luminance filter.** For dark-themed designs, search `luminance: "dark"` to get images that blend naturally with dark backgrounds.
- **Set `object-cover`** on image nodes so they fill their container without distortion.
- **Prefer real images over placeholders.** Instead of `<div className="w-full h-[400px] bg-gray-100 rounded-2xl" />`, search for a relevant image and use it.

---

## Shader Nodes & Visual Effects

Shader nodes are a powerful node type that combines **generative visuals**, **image/video processing**, and **visual effects** (ASCII, dither, halftone, glitch, art filters). They render via WebGL and can be placed anywhere in the design like any other node.

### Creating a Shader Node

Use `add_node` with `type: "shader"`:

```
add_node
  parentId: "<artboard-id>"
  type: "shader"
  shaderType: "meshGradient"
  className: "w-full h-[400px] rounded-2xl"
```

### Input Sources

| Input Type | Description | Key Properties |
|-----------|-------------|----------------|
| `shader` (default) | Generative WebGL visuals | `shaderType`, `shaderSettings` |
| `image` | Process an image through effects | `mediaInput: { mediaUrl, mediaType: "image", objectFit }` |
| `video` | Process a video through effects | `mediaInput: { mediaUrl, mediaType: "video" }` |
| `fill` | Solid color through effects | `fillColor: "#hex"` |

### Generative Shader Types (11)

| Shader | Description | Best For |
|--------|-------------|----------|
| `meshGradient` | Smooth organic color blending | Hero backgrounds, ambient visuals |
| `dotGrid` | Animated dot grid pattern | Tech/data aesthetics |
| `voronoi` | Cell-based organic pattern | Abstract textures |
| `liquidMetal` | Metallic fluid simulation | Premium/luxury visuals |
| `chrome` | Chrome reflection effect | Bold, reflective accents |
| `pulsar` | Radial pulsing energy | Dynamic, attention-grabbing |
| `blackHole` | Gravitational distortion | Dramatic dark visuals |
| `glass` | Frosted glass refraction | Subtle, elegant overlays |
| `spiral` | Spiraling motion pattern | Hypnotic, playful |
| `particles` | Floating particle system | Ambient, atmospheric |
| `fireworks` | Exploding particle bursts | Celebration, energy |

### Visual Effects (28)

Apply effects on top of any input source. Set `effectId` and `effectEnabled: true`:

```
add_node
  parentId: "<section-id>"
  type: "shader"
  inputType: "image"
  mediaInput: { mediaUrl: "<image-url>", mediaType: "image", objectFit: "cover" }
  effectId: "ascii-standard"
  effectEnabled: true
  className: "w-full h-[500px] rounded-2xl"
```

| Category | Effect IDs | Description |
|----------|-----------|-------------|
| **ASCII** | `ascii-standard`, `ascii-dense`, `ascii-minimal`, `ascii-blocks`, `ascii-braille`, `ascii-technical`, `ascii-matrix`, `ascii-hatching` | Convert visuals to ASCII character art |
| **Dither** | `dither-floyd-steinberg`, `dither-atkinson`, `dither-stucki`, `dither-sierra`, `dither-sierra-lite`, `dither-burkes`, `dither-jarvis-judice-ninke`, `dither-two-row-sierra`, `color-separation` | Retro dithering patterns |
| **Halftone** | `halftone-mono`, `halftone-cmyk` | Print-style dot patterns (mono or CMYK) |
| **Glitch** | `glitch-vhs`, `glitch-digital`, `glitch-weird`, `glitch-chromatic` | Digital corruption and distortion |
| **Art** | `art-kuwahara`, `art-crosshatch`, `art-lineart`, `art-engraving`, `art-stipple` | Painterly and sketch-style filters |

### Post-Process Stack

Layer additional effects with `postProcesses`: `scanlines`, `vignette`, `bloom`, `grain`, `noise`, `pixelate`, `wave`, `rgb-glitch`, `brightness-contrast`, `color-tint`, `sepia`, `grid`, `dot-screen`, `light-beams`, `warp`, `motion-blur`, `chromatic-aberration`, `curvature`.

```
postProcesses: [
  { type: "grain", enabled: true, settings: { intensity: 0.3 } },
  { type: "vignette", enabled: true, settings: { intensity: 0.5 } }
]
```

### Common Recipes

**ASCII art hero from an image:**
```
search_images  query: "city skyline"  orientation: "horizontal"  limit: 1
add_node
  parentId: "<artboard-id>"
  type: "shader"
  inputType: "image"
  mediaInput: { mediaUrl: "<url>", mediaType: "image", objectFit: "cover" }
  effectId: "ascii-standard"
  effectEnabled: true
  className: "w-full h-[500px]"
```

**Generative gradient background:**
```
add_node
  parentId: "<artboard-id>"
  type: "shader"
  shaderType: "meshGradient"
  className: "w-full h-[600px]"
```

**Halftone effect on a photo:**
```
add_node
  parentId: "<section-id>"
  type: "shader"
  inputType: "image"
  mediaInput: { mediaUrl: "<url>", mediaType: "image", objectFit: "cover" }
  effectId: "halftone-mono"
  effectEnabled: true
  className: "w-[400px] h-[400px] rounded-2xl"
```

---

## Node Types & HTML Tags

Every node maps 1:1 to an HTML element. The `type` determines behavior; the `tag` determines which HTML element renders.

| Type | Default Tag | All Valid Tags | Content Property |
|------|-------------|----------------|-----------------|
| **frame** | `div` | div, section, article, aside, main, nav, header, footer, ul, ol, li, figure, figcaption, form, table, thead, tbody, tr, th, td | (none — pure container) |
| **text** | `p` | p, span, h1, h2, h3, h4, h5, h6, blockquote, label, li | `textContent` |
| **image** | `img` | img | `src`, `alt`, `objectFit` |
| **button** | `button` | button, a, div | `textContent`, `variant` |
| **link** | `a` | a, button, div, span | `textContent`, `href`, `target` |
| **icon** | `svg` | svg | `iconName` (kebab-case), `iconLibrary`, `iconWeight` |
| **input** | `input` | input, textarea, select | `placeholder`, `inputType`, `label` |
| **video** | `video` | video | `src`, `poster`, `autoPlay`, `loop`, `muted` |
| **shader** | (WebGL) | — | `shaderType`, `inputType`, `effectId`, `effectEnabled`, `effectSettings`, `postProcesses` |
| **component** | `div` | div | `componentId`, `overrides` |

### Tag Determines Type in JSX

When writing JSX for `add_section`, the HTML tag determines the node type:

```
<div>       -> frame       <img>        -> image
<section>   -> frame       <button>     -> button
<nav>       -> frame       <a>          -> link
<header>    -> frame       <input>      -> input
<footer>    -> frame       <video>      -> video
<h1>-<h6>   -> text        <svg>        -> icon
<p>         -> text        <i>          -> icon
<span>      -> text        <Icon>       -> icon
                           <HeartIcon>  -> icon (name resolved from tag)
```

---

## The `add_section` JSX Format

This is the primary way to build layouts. Write standard JSX with HTML tags and Tailwind `className` attributes.

### Format Rules

1. **Use HTML tags** — div, section, nav, h1, p, img, button, a, etc.
2. **Style with className** — Tailwind CSS classes only
3. **Text goes between tags** — `<h1 className="...">Hello</h1>`
4. **Images are self-closing** — `<img src="https://..." alt="..." className="..." />`
5. **Use `data-id`** only when referencing existing nodes (from `get_document`)
6. **No React-specific syntax** — no `onClick`, `useState`, fragments, or components
7. **Icons use `<svg>` with the `icon` attribute** — `<svg icon="arrow-right" className="w-5 h-5 text-gray-600" />`

### Example: Complete Section

```jsx
<section className="flex flex-col items-center gap-12 px-16 py-24 bg-white w-full">
  <div className="flex flex-col items-center gap-4 max-w-2xl">
    <span className="text-sm font-semibold text-blue-600 uppercase tracking-widest">Why Choose Us</span>
    <h2 className="text-4xl font-bold text-gray-900 text-center">Everything you need to scale</h2>
    <p className="text-lg text-gray-500 text-center">Built for modern teams that move fast and ship faster.</p>
  </div>
  <div className="flex gap-8 w-full max-w-5xl">
    <div className="flex flex-col gap-3 p-8 bg-gray-50 rounded-2xl grow">
      <svg icon="lightning" className="w-8 h-8 text-blue-600" />
      <h3 className="text-lg font-semibold text-gray-900">Lightning Fast</h3>
      <p className="text-sm text-gray-500">Deploy in seconds, not hours. Our edge network ensures sub-50ms latency worldwide.</p>
    </div>
    <div className="flex flex-col gap-3 p-8 bg-gray-50 rounded-2xl grow">
      <svg icon="shield-check" className="w-8 h-8 text-blue-600" />
      <h3 className="text-lg font-semibold text-gray-900">Enterprise Security</h3>
      <p className="text-sm text-gray-500">SOC 2 certified with end-to-end encryption. Your data stays yours.</p>
    </div>
    <div className="flex flex-col gap-3 p-8 bg-gray-50 rounded-2xl grow">
      <svg icon="headset" className="w-8 h-8 text-blue-600" />
      <h3 className="text-lg font-semibold text-gray-900">24/7 Support</h3>
      <p className="text-sm text-gray-500">Our team is always on. Get answers in minutes, not days.</p>
    </div>
  </div>
</section>
```

---

## Icons

Efecto supports 4 icon libraries with ~9,400 icons total. Icons render as SVG and inherit color from `currentColor`.

### How to Write Icons in JSX

The canonical format:
```jsx
<svg icon="arrow-right" className="w-5 h-5 text-gray-600" />
```

- **`icon`** — Icon name in **kebab-case** (e.g. `arrow-right`, `magnifying-glass`, `google-logo`)
- **`className`** — Size via `w-*` / `h-*`, color via `text-*` classes
- **`iconLibrary`** — Optional: `phosphor` (default), `lucide`, `heroicons`, `radix`
- **`iconWeight`** — Optional: `thin`, `light`, `regular` (default), `bold`, `fill`, `duotone` (Phosphor only)

### Common Icon Names (Phosphor — default library)

| Category | Icons |
|----------|-------|
| **Arrows** | `arrow-right`, `arrow-left`, `arrow-up`, `arrow-down`, `caret-right`, `caret-down` |
| **Actions** | `check`, `x`, `plus`, `minus`, `magnifying-glass`, `funnel`, `pencil`, `trash` |
| **UI** | `gear`, `sliders`, `dots-three`, `list`, `squares-four`, `sidebar` |
| **Social** | `google-logo`, `apple-logo`, `github-logo`, `x-logo`, `linkedin-logo`, `discord-logo`, `facebook-logo`, `instagram-logo`, `youtube-logo` |
| **Objects** | `envelope`, `lock`, `eye`, `eye-slash`, `star`, `heart`, `user`, `users`, `house`, `lightning`, `fire`, `rocket` |
| **Media** | `image`, `camera`, `play`, `pause`, `microphone`, `speaker-high` |
| **Commerce** | `shopping-cart`, `credit-card`, `currency-dollar`, `receipt`, `package` |

### Icon Sizing Guide

```
w-3 h-3     /* 12px — tiny, inline indicators */
w-4 h-4     /* 16px — inline with small text */
w-5 h-5     /* 20px — standard UI icon */
w-6 h-6     /* 24px — default, comfortable */
w-8 h-8     /* 32px — feature icons, cards */
w-10 h-10   /* 40px — hero feature icons */
w-12 h-12   /* 48px — large decorative */
w-16 h-16   /* 64px — hero section centerpiece */
```

### Icon Patterns in Sections

**Feature card with icon:**
```jsx
<div className="flex flex-col gap-3 p-8 bg-gray-50 rounded-2xl grow">
  <svg icon="lightning" className="w-8 h-8 text-blue-600" />
  <h3 className="text-lg font-semibold text-gray-900">Lightning Fast</h3>
  <p className="text-sm text-gray-500">Deploy in seconds, not hours.</p>
</div>
```

**Button with icon (icons are siblings, not children):**
```jsx
<div className="flex items-center gap-2 bg-gray-900 text-white px-5 py-2.5 rounded-lg">
  <span className="text-sm font-medium">Get Started</span>
  <svg icon="arrow-right" className="w-4 h-4" />
</div>
```

**Social icons row:**
```jsx
<div className="flex items-center gap-4">
  <svg icon="x-logo" className="w-5 h-5 text-gray-400" />
  <svg icon="github-logo" className="w-5 h-5 text-gray-400" />
  <svg icon="linkedin-logo" className="w-5 h-5 text-gray-400" />
  <svg icon="discord-logo" className="w-5 h-5 text-gray-400" />
</div>
```

**Input with icon:**
```jsx
<div className="flex items-center gap-2 border border-gray-200 rounded-lg px-3 py-2 bg-white">
  <svg icon="magnifying-glass" className="w-4 h-4 text-gray-400" />
  <input placeholder="Search..." className="text-sm text-gray-900 grow" />
</div>
```

**Using a different library (Lucide):**
```jsx
<svg icon="check" iconLibrary="lucide" className="w-5 h-5 text-green-600" />
```

**Using a weight variant (Phosphor bold):**
```jsx
<svg icon="star" iconWeight="fill" className="w-5 h-5 text-yellow-500" />
```

---

## Tailwind CSS Patterns That Work

### Layout
```
flex                    /* Horizontal flex row */
flex flex-col           /* Vertical flex column */
items-center            /* Center cross-axis */
items-start             /* Align to start */
justify-between         /* Space between children */
justify-center          /* Center main-axis */
gap-2 gap-4 gap-8      /* Gap between flex children */
grow                    /* Fill remaining space (DO NOT use flex-1) */
```

### Spacing
```
p-4 p-6 p-8 p-12       /* Padding all sides */
px-6 py-4               /* Horizontal / vertical padding */
pt-8 pb-16              /* Top / bottom padding */
mx-auto                 /* Center horizontally */
```

### Sizing
```
w-full                  /* Full width of parent */
w-auto                  /* Width from content */
w-fit                   /* Shrink-wrap width */
w-[600px]               /* Fixed pixel width */
max-w-2xl max-w-5xl     /* Maximum width constraints */
min-h-screen            /* Minimum full viewport height */
h-[400px]               /* Fixed pixel height */
```

### Typography
```
text-xs text-sm text-base text-lg text-xl text-2xl text-3xl text-4xl text-5xl text-6xl text-7xl text-8xl text-9xl
font-light font-normal font-medium font-semibold font-bold font-extrabold font-black
leading-tight leading-snug leading-normal leading-relaxed
tracking-tight tracking-normal tracking-wide tracking-widest
text-left text-center text-right
uppercase lowercase capitalize
```

### Theme-Aware Colors (Preferred)

Efecto supports a semantic token system. When a theme is active, these classes auto-adapt to light/dark/custom modes:

```
/* Backgrounds */
bg-background              /* Page/artboard background */
bg-card                    /* Card surfaces */
bg-popover                 /* Popover/dropdown surfaces */
bg-primary                 /* Primary action backgrounds */
bg-secondary               /* Secondary surfaces */
bg-muted                   /* Muted/subtle backgrounds */
bg-accent                  /* Accent highlights */
bg-destructive             /* Destructive/error backgrounds */
bg-input                   /* Input field backgrounds */

/* Text */
text-foreground            /* Primary text */
text-primary-foreground    /* Text on primary backgrounds */
text-secondary-foreground  /* Text on secondary surfaces */
text-muted-foreground      /* Muted/secondary text */
text-accent-foreground     /* Text on accent backgrounds */
text-card-foreground       /* Text on cards */
text-popover-foreground    /* Text on popovers */
text-destructive-foreground /* Text on destructive backgrounds */

/* Borders */
border-border              /* Default border color */
ring-ring                  /* Focus ring color */
```

**When to use semantic vs hardcoded colors:**
- **Semantic** (`bg-primary`, `text-foreground`): UI components, buttons, cards, text — anything that should adapt with theme/mode changes
- **Hardcoded** (`bg-blue-500`, `text-white`): Decorative elements, brand-specific colors, illustrations, gradients

These classes map to CSS custom properties (`--background`, `--primary`, etc.) and automatically update when the theme or mode changes. shadcn is the default token system, but these are standard CSS variables — any design system that provides them will work.

### Colors (NAMED ONLY)

Use named Tailwind colors. **Never use arbitrary hex** like `bg-[#f9f9f9]` — it silently fails at runtime.

```
/* Backgrounds */
bg-white bg-black bg-gray-50 bg-gray-100 bg-gray-900 bg-neutral-950
bg-blue-50 bg-blue-500 bg-blue-600 bg-indigo-500 bg-purple-500
bg-green-50 bg-green-500 bg-red-50 bg-red-500 bg-amber-50 bg-yellow-400

/* Text */
text-white text-black text-gray-400 text-gray-500 text-gray-600 text-gray-700 text-gray-900
text-blue-600 text-blue-500 text-indigo-600 text-green-600 text-red-600

/* Borders */
border-gray-100 border-gray-200 border-gray-300 border-blue-500

/* If you MUST use a specific hex color, use inline style instead: */
style: { "background-color": "#f9f9f9" }
```

### Borders & Radius
```
border border-2                      /* Border width */
border-gray-200 border-gray-300      /* Border color */
rounded rounded-lg rounded-xl rounded-2xl rounded-full  /* Border radius */
```

### Effects
```
shadow-sm shadow shadow-md shadow-lg shadow-xl
opacity-50 opacity-75
ring-1 ring-2 ring-blue-500          /* Ring / outline */
```

### Gradients
```
bg-gradient-to-r from-blue-500 to-purple-500
bg-gradient-to-b from-white to-gray-50
bg-gradient-to-br from-indigo-500 via-purple-500 to-pink-500
```

### Fills (Preferred: `set_fill` tool)

For solid colors, gradients, and image backgrounds, prefer the `set_fill` tool over manually setting className/style. It handles the dual-write pattern automatically.

```
# Solid color
set_fill  targetId: "<id>"  fill: { type: "solid", color: "blue-500" }

# Linear gradient
set_fill  targetId: "<id>"  fill: { type: "gradient", gradient: { type: "linear", angle: 90, stops: [{ color: "#3b82f6", position: 0 }, { color: "#8b5cf6", position: 1 }] } }

# Radial gradient
set_fill  targetId: "<id>"  fill: { type: "gradient", gradient: { type: "radial", stops: [{ color: "#3b82f6", position: 0 }, { color: "#1e1b4b", position: 1 }] } }

# Image fill
set_fill  targetId: "<id>"  fill: { type: "image", url: "https://...", size: "cover", position: "center" }

# Clear fill
set_fill  targetId: "<id>"
```

Works on both nodes and artboards. For artboards, solid fills update `backgroundColor` for backward compatibility.

### Links & Anchors

Any node can be made clickable with the `link` property on `add_node`, `update_node`, or `batch_update`. Use `elementId` to make a node an anchor target.

| Link Type | Use Case | Required Fields |
|-----------|----------|-----------------|
| `url` | External URL | `url`, optional `target: "_blank"` |
| `page` | Navigate to another artboard | `pageId` (artboard ID) |
| `section` | Scroll to element anchor | `sectionId` (matches an `elementId`) |
| `email` | mailto link | `email` |
| `phone` | tel link | `phone` |

```
# Make a button link to an external URL
update_node  nodeId: "<btn-id>"  link: { type: "url", url: "https://example.com", target: "_blank" }

# Mark a section as an anchor target
update_node  nodeId: "<section-id>"  elementId: "pricing"

# Link a nav item to that anchor
update_node  nodeId: "<nav-link-id>"  link: { type: "section", sectionId: "pricing" }

# Link to another artboard (page navigation)
update_node  nodeId: "<link-id>"  link: { type: "page", pageId: "<artboard-id>" }

# Remove a link
update_node  nodeId: "<id>"  link: null
```

Works on any node type — frames, text, images, buttons. For published sites, `page` and `section` links become real navigation.

---

## Artboard Best Practices

### Standard Sizes
| Name | Width | Height | Use Case |
|------|-------|--------|----------|
| Desktop | 1440 | 900+ | Full website pages |
| Desktop (narrow) | 1280 | 800+ | Content-focused pages |
| Tablet | 768 | 1024 | Tablet responsive view |
| Mobile | 375 | 812 | iPhone / mobile view |

### Always Start With
```
create_artboard:
  name: "Homepage"
  width: 1440
  height: 900
  backgroundColor: "#ffffff"
  className: "flex flex-col"
```

- **`className: "flex flex-col"`** is essential — without it, children overlap at (0,0)
- **`backgroundColor`** is an inline style property, NOT a Tailwind class
- Height grows automatically when content overflows, but set an initial height

---

## Common Section Patterns

### Navigation Bar

```jsx
<nav className="flex items-center justify-between px-16 py-5 bg-white w-full border-b border-gray-100">
  <h2 className="text-xl font-bold text-gray-900">Acme</h2>
  <div className="flex items-center gap-8">
    <a className="text-sm font-medium text-gray-600" href="#">Features</a>
    <a className="text-sm font-medium text-gray-600" href="#">Pricing</a>
    <a className="text-sm font-medium text-gray-600" href="#">About</a>
    <button className="px-5 py-2.5 text-sm font-medium text-white bg-gray-900 rounded-lg">Get Started</button>
  </div>
</nav>
```

### Hero Section (Centered)

```jsx
<section className="flex flex-col items-center justify-center gap-8 px-16 py-32 bg-white w-full">
  <div className="flex flex-col items-center gap-6 max-w-3xl">
    <span className="px-4 py-1.5 text-xs font-semibold text-blue-700 bg-blue-50 rounded-full uppercase tracking-wider">Now in Beta</span>
    <h1 className="text-6xl font-bold text-gray-900 text-center leading-tight">Build beautiful websites at the speed of thought</h1>
    <p className="text-xl text-gray-500 text-center">The AI-powered design tool that turns ideas into production-ready pages. No code required.</p>
  </div>
  <div className="flex items-center gap-4">
    <button className="px-8 py-3.5 text-base font-semibold text-white bg-blue-600 rounded-xl">Start Free Trial</button>
    <button className="px-8 py-3.5 text-base font-semibold text-gray-700 bg-white border-2 border-gray-200 rounded-xl">Watch Demo</button>
  </div>
</section>
```

### Hero Section (Split — Text Left, Image Right)

```jsx
<section className="flex items-center gap-16 px-16 py-24 bg-white w-full">
  <div className="flex flex-col gap-6 grow">
    <h1 className="text-5xl font-bold text-gray-900 leading-tight">Ship your next product in record time</h1>
    <p className="text-lg text-gray-500">From prototype to production in minutes. Our platform handles the complexity so you can focus on what matters.</p>
    <div className="flex items-center gap-4">
      <button className="px-6 py-3 text-base font-semibold text-white bg-gray-900 rounded-lg">Get Started</button>
      <a className="text-base font-medium text-gray-600" href="#">Learn more</a>
    </div>
  </div>
  <img src="https://placehold.co/600x400" alt="Product screenshot" className="w-[560px] h-[380px] rounded-2xl object-cover bg-gray-100" />
</section>
```

### Feature Grid (3 columns)

```jsx
<section className="flex flex-col items-center gap-16 px-16 py-24 bg-gray-50 w-full">
  <div className="flex flex-col items-center gap-4 max-w-2xl">
    <h2 className="text-4xl font-bold text-gray-900 text-center">Powerful features for modern teams</h2>
    <p className="text-lg text-gray-500 text-center">Everything you need to design, build, and ship — all in one place.</p>
  </div>
  <div className="flex gap-8 w-full max-w-6xl">
    <div className="flex flex-col gap-4 p-8 bg-white rounded-2xl shadow-sm grow">
      <div className="flex items-center justify-center w-12 h-12 bg-blue-100 rounded-xl">
        <span className="text-blue-600 text-lg font-bold">1</span>
      </div>
      <h3 className="text-xl font-semibold text-gray-900">Real-Time Collaboration</h3>
      <p className="text-sm text-gray-500 leading-relaxed">Work together with your team in real time. See cursors, selections, and changes as they happen.</p>
    </div>
    <div className="flex flex-col gap-4 p-8 bg-white rounded-2xl shadow-sm grow">
      <div className="flex items-center justify-center w-12 h-12 bg-green-100 rounded-xl">
        <span className="text-green-600 text-lg font-bold">2</span>
      </div>
      <h3 className="text-xl font-semibold text-gray-900">One-Click Deploy</h3>
      <p className="text-sm text-gray-500 leading-relaxed">Push to production with a single click. Built-in CI/CD, preview deployments, and rollbacks.</p>
    </div>
    <div className="flex flex-col gap-4 p-8 bg-white rounded-2xl shadow-sm grow">
      <div className="flex items-center justify-center w-12 h-12 bg-purple-100 rounded-xl">
        <span className="text-purple-600 text-lg font-bold">3</span>
      </div>
      <h3 className="text-xl font-semibold text-gray-900">AI Assistant</h3>
      <p className="text-sm text-gray-500 leading-relaxed">Built-in AI that understands your codebase. Generate components, fix bugs, and refactor with natural language.</p>
    </div>
  </div>
</section>
```

### Testimonial / Social Proof

```jsx
<section className="flex flex-col items-center gap-12 px-16 py-24 bg-white w-full">
  <div className="flex flex-col items-center gap-6 max-w-2xl">
    <p className="text-2xl text-gray-700 text-center leading-relaxed italic">"This tool completely transformed our workflow. We shipped our redesign in two weeks instead of two months."</p>
    <div className="flex items-center gap-4">
      <div className="w-12 h-12 bg-gray-200 rounded-full"></div>
      <div className="flex flex-col">
        <span className="text-sm font-semibold text-gray-900">Sarah Chen</span>
        <span className="text-sm text-gray-500">VP of Design, Stripe</span>
      </div>
    </div>
  </div>
  <div className="flex items-center gap-12">
    <span className="text-sm font-medium text-gray-400">Trusted by</span>
    <span className="text-lg font-bold text-gray-300">Stripe</span>
    <span className="text-lg font-bold text-gray-300">Vercel</span>
    <span className="text-lg font-bold text-gray-300">Linear</span>
    <span className="text-lg font-bold text-gray-300">Notion</span>
    <span className="text-lg font-bold text-gray-300">Figma</span>
  </div>
</section>
```

### Pricing Cards

```jsx
<section className="flex flex-col items-center gap-12 px-16 py-24 bg-gray-50 w-full">
  <div className="flex flex-col items-center gap-4">
    <h2 className="text-4xl font-bold text-gray-900">Simple, transparent pricing</h2>
    <p className="text-lg text-gray-500">No surprise fees. Cancel anytime.</p>
  </div>
  <div className="flex gap-8 w-full max-w-4xl">
    <div className="flex flex-col gap-6 p-8 bg-white rounded-2xl border border-gray-200 grow">
      <div className="flex flex-col gap-2">
        <h3 className="text-lg font-semibold text-gray-900">Starter</h3>
        <div className="flex items-baseline gap-1">
          <span className="text-4xl font-bold text-gray-900">$19</span>
          <span className="text-sm text-gray-500">/month</span>
        </div>
        <p className="text-sm text-gray-500">Perfect for individuals and small projects.</p>
      </div>
      <button className="w-full py-3 text-sm font-semibold text-gray-700 bg-gray-100 rounded-lg">Get Started</button>
    </div>
    <div className="flex flex-col gap-6 p-8 bg-gray-900 rounded-2xl grow">
      <div className="flex flex-col gap-2">
        <h3 className="text-lg font-semibold text-white">Pro</h3>
        <div className="flex items-baseline gap-1">
          <span className="text-4xl font-bold text-white">$49</span>
          <span className="text-sm text-gray-400">/month</span>
        </div>
        <p className="text-sm text-gray-400">For growing teams that need more power.</p>
      </div>
      <button className="w-full py-3 text-sm font-semibold text-gray-900 bg-white rounded-lg">Get Started</button>
    </div>
    <div className="flex flex-col gap-6 p-8 bg-white rounded-2xl border border-gray-200 grow">
      <div className="flex flex-col gap-2">
        <h3 className="text-lg font-semibold text-gray-900">Enterprise</h3>
        <div className="flex items-baseline gap-1">
          <span className="text-4xl font-bold text-gray-900">$99</span>
          <span className="text-sm text-gray-500">/month</span>
        </div>
        <p className="text-sm text-gray-500">For large organizations with custom needs.</p>
      </div>
      <button className="w-full py-3 text-sm font-semibold text-gray-700 bg-gray-100 rounded-lg">Contact Sales</button>
    </div>
  </div>
</section>
```

### CTA (Call to Action)

```jsx
<section className="flex flex-col items-center gap-8 px-16 py-24 bg-gray-900 w-full">
  <h2 className="text-4xl font-bold text-white text-center">Ready to get started?</h2>
  <p className="text-lg text-gray-400 text-center max-w-xl">Join thousands of teams already building faster. Start your free trial today.</p>
  <div className="flex items-center gap-4">
    <button className="px-8 py-3.5 text-base font-semibold text-gray-900 bg-white rounded-xl">Start Free Trial</button>
    <button className="px-8 py-3.5 text-base font-semibold text-gray-300 bg-transparent border border-gray-700 rounded-xl">Talk to Sales</button>
  </div>
</section>
```

### Footer

```jsx
<footer className="flex items-center justify-between px-16 py-8 bg-white border-t border-gray-100 w-full">
  <span className="text-sm text-gray-500">2026 Acme Inc. All rights reserved.</span>
  <div className="flex items-center gap-6">
    <a className="text-sm text-gray-500" href="#">Privacy</a>
    <a className="text-sm text-gray-500" href="#">Terms</a>
    <a className="text-sm text-gray-500" href="#">Contact</a>
  </div>
</footer>
```

### Stats / Metrics Row

```jsx
<section className="flex items-center justify-center gap-16 px-16 py-16 bg-blue-600 w-full">
  <div className="flex flex-col items-center gap-2">
    <span className="text-4xl font-bold text-white">10K+</span>
    <span className="text-sm text-blue-200">Active Users</span>
  </div>
  <div className="flex flex-col items-center gap-2">
    <span className="text-4xl font-bold text-white">99.9%</span>
    <span className="text-sm text-blue-200">Uptime SLA</span>
  </div>
  <div className="flex flex-col items-center gap-2">
    <span className="text-4xl font-bold text-white">50ms</span>
    <span className="text-sm text-blue-200">Avg Latency</span>
  </div>
  <div className="flex flex-col items-center gap-2">
    <span className="text-4xl font-bold text-white">4.9/5</span>
    <span className="text-sm text-blue-200">User Rating</span>
  </div>
</section>
```

---

## Tips, Gotchas, and Best Practices

### The Golden Rules

1. **Always `get_document` first** before modifying an existing design. You need the `data-id` attributes.
2. **Use `get_selection`** when the user asks you to edit or add something "here" — it returns the selected nodes with their JSX subtrees so you can make contextual edits.
3. **Never guess node IDs** — always read them from `get_document`, `get_selection`, or `find_nodes`.
3. **Use `add_section` with JSX** for building structures. It is far more efficient than `add_node` one element at a time.
4. **Use `batch_update`** for multiple small changes. One call instead of many.
5. **Prefer updating over deleting and recreating.** Use `update_node` or `update_class`.

### Critical Gotchas

**Artboard className must include `flex flex-col`**
Without it, children overlap at (0,0). This is the #1 mistake.

**Artboard backgroundColor is a property, NOT a className**
```
WRONG:  className: "bg-gray-900 flex flex-col"
RIGHT:  backgroundColor: "#1a1a1a", className: "flex flex-col"
```

**Never use arbitrary hex colors in className**
Tailwind 4 generates CSS at build time. Dynamic classes like `bg-[#f9f9f9]` have no CSS at runtime and are invisible.
```
WRONG:  className: "bg-[#f9f9f9] text-[#333333]"
RIGHT:  className: "bg-gray-50 text-gray-700"
ALSO OK: style: { "background-color": "#f9f9f9", "color": "#333333" }
```
Note: The system auto-converts arbitrary hex to inline styles, but prefer named colors.

**`flex-1` does not work — use `grow` instead**
`flex-1` gets classified as unmapped by the Tailwind bridge. Use `grow` for the same effect.

**Frame nodes have no textContent**
Frame types (div, section, nav, header, footer) cannot hold text directly. Use text nodes (h1, p, span) inside them.

**Button nodes ignore children**
The button renderer only uses `textContent`. Do not nest icons or other elements inside buttons — wrap them in a flex container instead:
```jsx
<div className="flex items-center gap-2 bg-blue-600 text-white px-5 py-2.5 rounded-lg">
  <span className="text-sm font-medium">Continue</span>
  <svg icon="arrow-right" className="w-4 h-4" />
</div>
```

**Top-level sections need `w-full`**
The auto-fixer adds this, but always include `w-full` on direct children of the artboard so they span the full width.

**Images without `src` get a placeholder**
If you omit `src`, the system inserts `https://placehold.co/600x400`. Always provide a real src for production designs.

### Design Tips

- **Max-width containers**: Use `max-w-5xl` or `max-w-6xl` centered with content inside for readable text widths
- **Consistent spacing**: Use the Tailwind spacing scale (4, 6, 8, 12, 16, 24 are common section padding values)
- **Color hierarchy**: Use gray-900 for headings, gray-500 for body text, gray-400 for secondary text
- **Section rhythm**: Alternate between white and gray-50 backgrounds for visual separation
- **Button hierarchy**: Primary = solid dark, Secondary = outline or light background

---

## Complete Recipe: SaaS Landing Page

Build a complete SaaS landing page from scratch — step by step.

### Step 1: Create Session and Artboard

```
create_session
  label: "SaaS Landing Page"
```

Wait for browser connection (`session_status`), then:

```
create_artboard:
  name: "Landing Page"
  width: 1440
  height: 900
  backgroundColor: "#ffffff"
  className: "flex flex-col"
```

### Step 2: Navigation

```
add_section:
  parentId: "<artboard-id>"
  jsx: |
    <nav className="flex items-center justify-between px-16 py-5 bg-white w-full border-b border-gray-100">
      <div className="flex items-center gap-2">
        <div className="w-8 h-8 bg-blue-600 rounded-lg"></div>
        <h2 className="text-lg font-bold text-gray-900">Launchpad</h2>
      </div>
      <div className="flex items-center gap-8">
        <a className="text-sm font-medium text-gray-600" href="#">Product</a>
        <a className="text-sm font-medium text-gray-600" href="#">Features</a>
        <a className="text-sm font-medium text-gray-600" href="#">Pricing</a>
        <a className="text-sm font-medium text-gray-600" href="#">Docs</a>
      </div>
      <div className="flex items-center gap-3">
        <a className="text-sm font-medium text-gray-600" href="#">Sign In</a>
        <button className="px-5 py-2 text-sm font-medium text-white bg-blue-600 rounded-lg">Start Free</button>
      </div>
    </nav>
```

### Step 3: Hero

```
add_section:
  parentId: "<artboard-id>"
  jsx: |
    <section className="flex flex-col items-center gap-10 px-16 py-32 bg-white w-full">
      <div className="flex flex-col items-center gap-6 max-w-3xl">
        <span className="px-4 py-1.5 text-xs font-semibold text-blue-700 bg-blue-50 rounded-full uppercase tracking-wider">New: AI-Powered Workflows</span>
        <h1 className="text-6xl font-extrabold text-gray-900 text-center leading-tight tracking-tight">Ship products faster than ever before</h1>
        <p className="text-xl text-gray-500 text-center leading-relaxed">The all-in-one platform for modern product teams. Design, build, test, and deploy from a single dashboard.</p>
      </div>
      <div className="flex items-center gap-4">
        <button className="px-8 py-4 text-base font-semibold text-white bg-blue-600 rounded-xl">Start Free Trial</button>
        <button className="px-8 py-4 text-base font-semibold text-gray-700 bg-white border-2 border-gray-200 rounded-xl">Watch Demo</button>
      </div>
      <p className="text-sm text-gray-400">Free for 14 days. No credit card required.</p>
    </section>
```

### Step 4: Social Proof Bar

```
add_section:
  parentId: "<artboard-id>"
  jsx: |
    <section className="flex items-center justify-center gap-12 px-16 py-12 bg-gray-50 border-y border-gray-100 w-full">
      <span className="text-sm text-gray-400">Trusted by leading teams at</span>
      <span className="text-lg font-bold text-gray-300">Stripe</span>
      <span className="text-lg font-bold text-gray-300">Vercel</span>
      <span className="text-lg font-bold text-gray-300">Linear</span>
      <span className="text-lg font-bold text-gray-300">Notion</span>
      <span className="text-lg font-bold text-gray-300">Figma</span>
    </section>
```

### Step 5: Feature Grid

```
add_section:
  parentId: "<artboard-id>"
  jsx: |
    <section className="flex flex-col items-center gap-16 px-16 py-24 bg-white w-full">
      <div className="flex flex-col items-center gap-4 max-w-2xl">
        <h2 className="text-4xl font-bold text-gray-900 text-center">Built for speed, designed for scale</h2>
        <p className="text-lg text-gray-500 text-center">Powerful tools that grow with your team. No compromises.</p>
      </div>
      <div className="flex gap-6 w-full max-w-5xl">
        <div className="flex flex-col gap-4 p-8 bg-gray-50 rounded-2xl grow">
          <div className="flex items-center justify-center w-12 h-12 bg-blue-100 rounded-xl">
            <span className="text-blue-600 font-bold">1</span>
          </div>
          <h3 className="text-lg font-semibold text-gray-900">Visual Editor</h3>
          <p className="text-sm text-gray-500 leading-relaxed">Drag-and-drop interface with pixel-perfect precision. Design at the speed of thought.</p>
        </div>
        <div className="flex flex-col gap-4 p-8 bg-gray-50 rounded-2xl grow">
          <div className="flex items-center justify-center w-12 h-12 bg-green-100 rounded-xl">
            <span className="text-green-600 font-bold">2</span>
          </div>
          <h3 className="text-lg font-semibold text-gray-900">Instant Deploy</h3>
          <p className="text-sm text-gray-500 leading-relaxed">One-click deployment to our global CDN. Preview environments for every branch.</p>
        </div>
        <div className="flex flex-col gap-4 p-8 bg-gray-50 rounded-2xl grow">
          <div className="flex items-center justify-center w-12 h-12 bg-purple-100 rounded-xl">
            <span className="text-purple-600 font-bold">3</span>
          </div>
          <h3 className="text-lg font-semibold text-gray-900">AI Copilot</h3>
          <p className="text-sm text-gray-500 leading-relaxed">AI that understands your design system. Generate components, layouts, and content automatically.</p>
        </div>
      </div>
    </section>
```

### Step 6: CTA

```
add_section:
  parentId: "<artboard-id>"
  jsx: |
    <section className="flex flex-col items-center gap-8 px-16 py-24 bg-gray-900 w-full">
      <h2 className="text-4xl font-bold text-white text-center">Start building today</h2>
      <p className="text-lg text-gray-400 text-center max-w-xl">Join 10,000+ teams already shipping faster with Launchpad. Free trial, no strings attached.</p>
      <div className="flex items-center gap-4">
        <button className="px-8 py-4 text-base font-semibold text-gray-900 bg-white rounded-xl">Start Free Trial</button>
        <button className="px-8 py-4 text-base font-semibold text-gray-300 bg-transparent border border-gray-700 rounded-xl">Talk to Sales</button>
      </div>
    </section>
```

### Step 7: Footer

```
add_section:
  parentId: "<artboard-id>"
  jsx: |
    <footer className="flex items-center justify-between px-16 py-8 bg-gray-900 border-t border-gray-800 w-full">
      <div className="flex items-center gap-2">
        <div className="w-6 h-6 bg-blue-600 rounded-md"></div>
        <span className="text-sm font-semibold text-gray-400">Launchpad</span>
      </div>
      <span className="text-sm text-gray-500">2026 Launchpad Inc. All rights reserved.</span>
      <div className="flex items-center gap-6">
        <a className="text-sm text-gray-500" href="#">Privacy</a>
        <a className="text-sm text-gray-500" href="#">Terms</a>
        <a className="text-sm text-gray-500" href="#">Status</a>
      </div>
    </footer>
```

### Step 8: Verify and Refine

Call `get_document` to verify the structure. Use `batch_update` for any tweaks — adjust colors, spacing, or text content across multiple nodes at once.

---

## Responsive Design Workflow

To create responsive variations:

1. Build the desktop design (1440px) first
2. Use `duplicate_artboard` to clone it for tablet (768px) and mobile (375px)
3. Use `batch_update` to adjust:
   - Switch horizontal flex rows to `flex flex-col` for stacking
   - Reduce padding: `px-16` to `px-6`, `py-24` to `py-16`
   - Scale down text: `text-6xl` to `text-4xl`, `text-4xl` to `text-2xl`
   - Change gap values: `gap-8` to `gap-4`
   - Change grid layouts: 3-column features to 1-column stack

---

## Dark Mode Design

**Preferred: Use the theme system** — it handles light/dark automatically:

1. Call `set_theme` with a preset (e.g. `presetId: "default"`) — light + dark modes are included
2. Call `set_theme_mode` with `mode: "dark"` — artboards switch to dark tokens
3. Use semantic classes (`bg-background`, `text-foreground`, `bg-card`, etc.) — colors auto-correct for the active mode
4. Switch back with `set_theme_mode` `mode: "light"` at any time

**Manual fallback** (for custom non-theme designs without semantic tokens):

1. Set artboard `backgroundColor: "#0a0a0a"` or `"#111111"`
2. Use inverted color scale:
   - Headings: `text-white` or `text-gray-100`
   - Body: `text-gray-400`
   - Secondary: `text-gray-500`
   - Backgrounds: `bg-gray-900`, `bg-gray-800`, `bg-neutral-900`
   - Borders: `border-gray-800`
   - Cards: `bg-gray-900` with `border border-gray-800`
3. Buttons: `bg-white text-gray-900` for primary, `border border-gray-700 text-gray-300` for secondary

---

## Theme System

Efecto's theme system lets you define design tokens (colors, radius) that power semantic Tailwind classes. Themes support multiple modes (light, dark, or custom) and all artboards update live when you switch.

### Theme Tools

| Tool | Description |
|------|-------------|
| `get_theme` | Returns current theme: id, name, modes, activeMode, and resolved active tokens |
| `set_theme` | Apply a preset, import CSS, or provide token objects directly |
| `set_theme_mode` | Switch the active mode (e.g. "light" -> "dark") |
| `reset_theme` | Remove theme entirely — back to raw Tailwind colors with no semantic tokens |

### Apply a Preset

```
set_theme
  presetId: "blue"
```

Available presets: `default`, `zinc`, `slate`, `rose`, `blue`, `green`, `violet`, `orange`. Each includes light and dark modes.

### Switch Mode

```
set_theme_mode
  mode: "dark"
```

### Bring Your Own Tokens

Agents can import design tokens from **any source** — a user's repo `globals.css`, a shadcn theme, Radix colors, Open Props, or hand-crafted values. As long as the CSS uses `--variable-name: value;` declarations, they work.

**Import from a CSS file** (e.g. the user's `globals.css`):

```
set_theme
  css: ":root { --background: #ffffff; --foreground: #0a0a0a; --primary: #e11d48; --primary-foreground: #fff1f2; --secondary: #f5f5f5; --secondary-foreground: #171717; --muted: #f5f5f5; --muted-foreground: #737373; --border: #e5e5e5; --ring: #fb7185; --radius: 0.5rem; } .dark { --background: #0a0a0a; --foreground: #fafafa; --primary: #fb7185; --primary-foreground: #1c1917; --secondary: #262626; --secondary-foreground: #fafafa; --muted: #262626; --muted-foreground: #a3a3a3; --border: #404040; --ring: #e11d48; }"
```

If the user says "use my brand colors" or "match my repo", read their CSS file and pass it via the `css` parameter.

**Create tokens from scratch** (for a brand):

```
set_theme
  modes: {
    "light": { "primary": "#6d28d9", "primaryForeground": "#f5f3ff", "background": "#fafafa", "foreground": "#1a1a1a" },
    "dark": { "primary": "#a78bfa", "primaryForeground": "#1a1a1a", "background": "#0a0a0a", "foreground": "#fafafa" }
  }
```

Missing token keys automatically inherit from defaults.

### Build with Tokens

Once a theme is active, use semantic classes in your JSX:

```jsx
<div className="bg-background text-foreground">
  <div className="bg-card border border-border rounded-lg p-6">
    <h2 className="text-foreground text-xl font-bold">Card Title</h2>
    <p className="text-muted-foreground">Supporting text</p>
    <button className="bg-primary text-primary-foreground px-4 py-2 rounded-md">Action</button>
  </div>
</div>
```

These classes render correctly in any mode — switch from light to dark and all colors update.

### Supported Token Keys

| CSS Variable | Token Key | Usage |
|-------------|-----------|-------|
| `--background` | `background` | Page background (`bg-background`) |
| `--foreground` | `foreground` | Primary text (`text-foreground`) |
| `--card` | `card` | Card surface (`bg-card`) |
| `--card-foreground` | `cardForeground` | Card text (`text-card-foreground`) |
| `--popover` | `popover` | Popover surface (`bg-popover`) |
| `--popover-foreground` | `popoverForeground` | Popover text (`text-popover-foreground`) |
| `--primary` | `primary` | Primary action (`bg-primary`) |
| `--primary-foreground` | `primaryForeground` | Text on primary (`text-primary-foreground`) |
| `--secondary` | `secondary` | Secondary surface (`bg-secondary`) |
| `--secondary-foreground` | `secondaryForeground` | Text on secondary (`text-secondary-foreground`) |
| `--muted` | `muted` | Muted surface (`bg-muted`) |
| `--muted-foreground` | `mutedForeground` | Muted text (`text-muted-foreground`) |
| `--accent` | `accent` | Accent surface (`bg-accent`) |
| `--accent-foreground` | `accentForeground` | Text on accent (`text-accent-foreground`) |
| `--destructive` | `destructive` | Error/danger (`bg-destructive`) |
| `--destructive-foreground` | `destructiveForeground` | Text on destructive (`text-destructive-foreground`) |
| `--border` | `border` | Default borders (`border-border`) |
| `--input` | `input` | Input borders (`bg-input`) |
| `--ring` | `ring` | Focus rings (`ring-ring`) |
| `--radius` | `radius` | Border radius base |
