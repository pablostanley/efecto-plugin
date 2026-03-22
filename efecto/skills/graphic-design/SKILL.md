---
name: efecto-graphic-design
description: Design graphic assets with Efecto — presentations, pitch decks, event posters, email headers, blog images, open graph cards, business cards, resumes, menus, infographics, invitations, newsletters, and documents. Use when asked to "design a poster", "create a pitch deck", "make a presentation", "design a business card", or any graphic design task. Requires Efecto MCP server.
metadata:
  mcp-server: efecto
  author: pablostanley
  version: "1.0.0"
  argument-hint: <design-description>
---

# Efecto — Graphic Design Guide

Design graphic assets — presentations, pitch decks, event posters, email headers, blog images, OG cards, business cards, resumes, menus, infographics, invitations, newsletters, and documents — using the Efecto design tool.

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

### How It Works

1. Your agent creates an Efecto session via MCP (`create_session`)
2. You open the returned design URL in your browser
3. The agent pushes design commands — you see every change live
4. Iterate with natural language until the design is perfect

---

## Using Efecto Tools — Quick Reference

### Session Workflow

Every design starts with a session:

```
create_session  label: "Pitch Deck"
```
Returns `{ sessionId, designUrl }`. Tell the user to open the URL, then poll `session_status` until `browserConnected: true`.

### Building Designs

The primary workflow is: **create artboard → add sections with JSX → refine with updates**.

```
# 1. Create an artboard
create_artboard  name: "Slide 1"  width: 1920  height: 1080  backgroundColor: "#1a1a1a"  className: "flex flex-col"

# 2. Add content with JSX (most efficient way to build)
add_section  parentId: "<artboard-id>"  jsx: '<section className="flex flex-col items-center justify-center gap-6 p-24 w-full h-full">
  <h1 className="text-7xl font-extrabold text-white text-center">Your Title Here</h1>
  <p className="text-2xl text-gray-400">Subtitle text</p>
</section>'

# 3. Read current state to get node IDs
get_document

# 4. Fine-tune with batch updates
batch_update  updates: [
  { nodeId: "abc", className: "text-8xl font-black text-white" },
  { nodeId: "def", textContent: "New text" }
]
```

### All 46 Tools

| Category | Tools |
|----------|-------|
| **Session** | `create_session`, `wait_for_connection`, `session_status`, `close_session` |
| **Reading** | `get_document`, `get_selection`, `get_node_tree`, `list_artboards`, `find_nodes` |
| **Creating** | `create_artboard`, `add_section`, `add_node` |
| **Modifying** | `update_node`, `update_class`, `update_artboard`, `batch_update`, `replace_section` |
| **Organizing** | `move_node`, `duplicate_node`, `duplicate_artboard`, `group_nodes`, `ungroup_node`, `reorder_node` |
| **Selection** | `select_nodes`, `deselect_all`, `set_visibility`, `delete_nodes`, `delete_artboard` |
| **Alignment** | `align_nodes`, `distribute_nodes` |
| **Fill & Export** | `set_fill`, `export_image` |
| **Viewport** | `zoom_to_artboard`, `zoom_to_fit`, `set_viewport`, `move_artboard` |
| **Document** | `rename_document`, `new_document` |
| **History** | `undo`, `redo` |
| **Theme** | `get_theme`, `set_theme`, `set_theme_mode`, `reset_theme` |
| **Quality** | `audit_design`, `repair_design` |

### JSX Format for `add_section`

Write standard HTML tags with Tailwind `className`. The tag determines the node type:

```
<div>/<section>/<nav>  → frame       <img>     → image
<h1>-<h6>/<p>/<span>   → text        <button>  → button
<a>                    → link        <svg>     → icon
<input>                → input       <video>   → video
```

Icons: `<svg icon="arrow-right" className="w-5 h-5 text-gray-600" />`

### Critical Gotchas

- **Artboard `className` must include `flex flex-col`** — without it, children overlap at (0,0)
- **Artboard `backgroundColor` is a property, NOT a className** — use `backgroundColor: "#1a1a1a"`, not `className: "bg-gray-900"`
- **Never use arbitrary hex in className** — `bg-[#f9f9f9]` silently fails. Use named colors (`bg-gray-50`) or inline `style`
- **Use `grow` instead of `flex-1`** — `flex-1` doesn't work in Efecto
- **Buttons ignore children** — use a flex container with `<span>` + `<svg>` instead
- **Always add `w-full`** on direct children of artboards

---

## Images — Use Real Photos, Not Placeholders

Use `search_images` to find free, high-quality stock photos from Lummi. **No session required** — call it anytime.

```
search_images
  query: "conference stage audience"
  orientation: "horizontal"
  luminance: "dark"
  limit: 4
```

Then apply images to your designs:

- **Image node**: `add_node` with `type: "image"`, `src: "<url>"`, `alt: "..."`, `className: "w-full h-[400px] object-cover rounded-2xl"`
- **Background fill**: `set_fill` with `fill: { type: "image", url: "<url>", size: "cover" }`
- **In JSX**: `<img src="<url>" alt="..." className="w-full h-[400px] object-cover rounded-2xl" />`

**Tips for graphic design**: Match `orientation` to your artboard (vertical for posters/stories, horizontal for banners/presentations, square for social cards). Use `luminance: "dark"` for dark-themed designs. Search before designing — build layouts around real images, not empty gray boxes.

---

## Shader Nodes & Visual Effects

Add visual effects to graphic designs with shader nodes. Use `add_node` with `type: "shader"`.

### Generative Backgrounds

Create eye-catching backgrounds without images:

```
add_node
  parentId: "<artboard-id>"
  type: "shader"
  shaderType: "meshGradient"
  className: "w-full h-full"
```

**Shader types**: `meshGradient` (organic blending), `dotGrid` (tech/data), `voronoi` (organic cells), `liquidMetal` (premium), `chrome` (reflective), `pulsar` (energy), `blackHole` (dramatic), `glass` (frosted), `spiral` (playful), `particles` (atmospheric), `fireworks` (celebration).

### Apply Effects to Images

Process photos with ASCII, dither, halftone, glitch, or art effects:

```
search_images  query: "portrait"  orientation: "vertical"  limit: 1
add_node
  parentId: "<artboard-id>"
  type: "shader"
  inputType: "image"
  mediaInput: { mediaUrl: "<url>", mediaType: "image", objectFit: "cover" }
  effectId: "halftone-mono"
  effectEnabled: true
  className: "w-full h-full"
```

**Effect categories**: ASCII (`ascii-standard`, `ascii-dense`, `ascii-minimal`, `ascii-blocks`), Dither (`dither-floyd-steinberg`, `dither-atkinson`), Halftone (`halftone-mono`, `halftone-cmyk`), Glitch (`glitch-vhs`, `glitch-digital`), Art (`art-kuwahara`, `art-crosshatch`, `art-lineart`, `art-engraving`, `art-stipple`).

### Post-Processes

Stack additional effects: `grain`, `vignette`, `bloom`, `scanlines`, `noise`, `pixelate`, `sepia`, `color-tint`.

```
add_node
  parentId: "<id>"
  type: "shader"
  shaderType: "meshGradient"
  postProcesses: [
    { type: "grain", enabled: true, settings: { intensity: 0.3 } },
    { type: "vignette", enabled: true, settings: { intensity: 0.5 } }
  ]
  className: "w-full h-full"
```

**Best for**: Presentation backgrounds, poster visuals, event graphics, blog hero images, OG cards with distinctive visual treatments.

---

## Mindset: Design for the Context

Every graphic has a context that dictates the rules:

- **Presentation slides** are projected or screen-shared — big text, simple layouts, one idea per slide
- **Event posters** are scanned quickly — bold headline, essential details (date, place, CTA), strong visual identity
- **Email headers** are 600px wide and compete with dozens of other emails — punchy, branded, action-oriented
- **Blog hero images** set the tone for an article — atmospheric, typographic, or illustrative
- **OG/share cards** are the first impression of a link — they need to work at thumbnail size AND full size

The common thread: **less is more.** Every element must earn its place.

---

## Presentations & Pitch Decks

### Artboard Sizes

| Format | Width | Height | Use Case |
|--------|-------|--------|----------|
| **Standard (16:9)** | 1920 | 1080 | Presentations, screen share |
| **Widescreen (16:10)** | 1920 | 1200 | Mac-native presentations |
| **Classic (4:3)** | 1440 | 1080 | Traditional slides, projectors |

### Slide Design Rules

1. **One idea per slide.** If you need bullet points, each bullet should be its own slide.
2. **30pt rule.** At 1920px width, nothing smaller than `text-3xl` (`30px`). If projected, even `text-4xl` is the minimum.
3. **6 words per line, 6 lines max.** Less is better. If you have a paragraph, you have a document, not a slide.
4. **Full-bleed visuals.** When using an image, let it fill the entire slide. Overlay text on a solid dark block (not a gradient — use solid colors).
5. **Consistent grid.** Pick a layout system and stick with it across all slides. Title always in the same position.
6. **Slide numbers.** Subtle but present — helps navigation during Q&A.

### Slide Types

#### Title Slide

```jsx
<section className="flex flex-col items-center justify-center gap-6 p-24 bg-gray-900 w-full h-full">
  <span className="text-xl font-semibold text-blue-400 uppercase tracking-widest">Q4 2026 Update</span>
  <h1 className="text-7xl font-extrabold text-white text-center leading-none tracking-tight">Building the Future of Design</h1>
  <p className="text-2xl text-gray-400 font-medium">Acme Inc. / October 2026</p>
</section>
```

#### Section Divider

```jsx
<section className="flex flex-col items-start justify-center gap-4 px-24 py-16 bg-blue-600 w-full h-full">
  <span className="text-xl font-bold text-blue-200 uppercase tracking-widest">Part 02</span>
  <h2 className="text-6xl font-extrabold text-white leading-tight">Product Strategy</h2>
</section>
```

#### Content Slide (Text + Visual)

```jsx
<section className="flex items-center gap-16 px-24 py-16 bg-white w-full h-full">
  <div className="flex flex-col gap-6 grow">
    <h2 className="text-4xl font-bold text-gray-900 leading-snug">Revenue grew 3x in 12 months</h2>
    <p className="text-2xl text-gray-500 leading-relaxed">We hit $10M ARR ahead of schedule, driven by enterprise adoption and a 95% retention rate.</p>
  </div>
  <div className="w-[600px] h-[500px] bg-gray-100 rounded-3xl"></div>
</section>
```

#### Big Number / Stat Slide

```jsx
<section className="flex flex-col items-center justify-center gap-6 p-24 bg-gray-900 w-full h-full">
  <span className="text-9xl font-black text-blue-400">3x</span>
  <h2 className="text-4xl font-bold text-white text-center">Revenue growth in 12 months</h2>
  <p className="text-xl text-gray-500">$3.2M to $10M ARR</p>
</section>
```

#### Quote Slide

```jsx
<section className="flex flex-col items-center justify-center gap-8 px-32 py-24 bg-gray-50 w-full h-full">
  <p className="text-4xl text-gray-700 text-center leading-relaxed italic">"The best tool we've adopted this year. Our design velocity doubled."</p>
  <div className="flex items-center gap-4">
    <div className="w-16 h-16 bg-gray-300 rounded-full"></div>
    <div className="flex flex-col">
      <span className="text-xl font-semibold text-gray-900">Jamie Rivera</span>
      <span className="text-lg text-gray-500">Head of Design, Linear</span>
    </div>
  </div>
</section>
```

#### Closing / CTA Slide

```jsx
<section className="flex flex-col items-center justify-center gap-8 p-24 bg-gray-900 w-full h-full">
  <h2 className="text-6xl font-extrabold text-white text-center">Let's build together</h2>
  <p className="text-2xl text-gray-400 text-center">hello@acme.com</p>
  <div className="flex items-center gap-2">
    <div className="w-10 h-10 bg-blue-600 rounded-lg"></div>
    <span className="text-xl font-bold text-gray-400">Acme Inc.</span>
  </div>
</section>
```

### Pitch Deck Flow (10 slides)

1. **Title** — Company name, tagline, date
2. **Problem** — What pain exists (one stat or quote)
3. **Solution** — What you do (one sentence + visual)
4. **Demo / Product** — Screenshot or mockup (full-bleed)
5. **Market** — TAM/SAM/SOM or market size stat
6. **Traction** — Key metrics (big numbers)
7. **Business Model** — How you make money (simple diagram)
8. **Team** — Founders + key hires (photos + one-line bios)
9. **Ask** — What you need (funding amount, partnerships)
10. **Closing** — Contact info, thank you

---

## Event Posters & Flyers

### Artboard Sizes

| Format | Width | Height | Use Case |
|--------|-------|--------|----------|
| **Digital Poster** | 1080 | 1350 | Online events, sharing |
| **A4 Portrait** | 794 | 1123 | Print posters (210x297mm at 96dpi) |
| **Letter Portrait** | 816 | 1056 | US letter print (8.5x11in at 96dpi) |
| **Wide Banner** | 1920 | 600 | Website event banners |
| **Square** | 1080 | 1080 | Multi-purpose, adaptable |

### Event Poster Hierarchy

Every event poster needs these 4 elements in this priority order:

1. **What** — The event name (largest text, the visual anchor)
2. **When** — Date and time (second-largest, must be instantly readable)
3. **Where** — Location or "Online" (clear and prominent)
4. **CTA** — "Register now" / "Get tickets" / "RSVP" (button or highlighted text)

Everything else (speakers, sponsors, descriptions) is secondary.

### Event Poster Template

```jsx
<section className="flex flex-col justify-between p-16 bg-gray-900 w-full h-full">
  <div className="flex items-center justify-between">
    <span className="text-lg font-bold text-gray-400 uppercase tracking-widest">Design Conf 2026</span>
    <div className="w-12 h-12 bg-blue-500 rounded-xl"></div>
  </div>
  <div className="flex flex-col gap-6">
    <h1 className="text-7xl font-black text-white leading-none">The Future of Interface Design</h1>
    <div className="flex flex-col gap-2">
      <p className="text-3xl font-bold text-blue-400">March 15, 2026</p>
      <p className="text-2xl text-gray-400">San Francisco / Moscone Center</p>
    </div>
    <button className="w-fit px-10 py-4 text-xl font-bold text-gray-900 bg-white rounded-2xl">Get Tickets</button>
  </div>
</section>
```

### Conference Talk Slide

```jsx
<section className="flex items-end gap-8 p-16 bg-gray-900 w-full h-full">
  <div className="flex flex-col gap-4 grow">
    <span className="text-lg font-semibold text-purple-400 uppercase tracking-wider">Keynote</span>
    <h1 className="text-5xl font-extrabold text-white leading-tight">Designing with AI: What Changes and What Doesn't</h1>
    <div className="flex items-center gap-3">
      <div className="w-12 h-12 bg-gray-700 rounded-full"></div>
      <div className="flex flex-col">
        <span className="text-lg font-semibold text-white">Pablo Stanley</span>
        <span className="text-lg text-gray-500">Founder, Efecto</span>
      </div>
    </div>
  </div>
</section>
```

---

## Email Headers & Newsletter Graphics

### Artboard Sizes

| Format | Width | Height | Use Case |
|--------|-------|--------|----------|
| **Email Header** | 600 | 200 | Top-of-email banner (max-width 600px) |
| **Email Hero** | 600 | 400 | Hero image in newsletter |
| **Wide Email Hero** | 1200 | 400 | Retina email hero (scales to 600px) |

### Email Design Rules

- **600px max width** is the universal email constraint. Design at 600px or 1200px (retina 2x).
- **Simple layouts only.** Email rendering engines are stuck in 2005. Stick to single-column or two-column max.
- **Big, obvious CTA.** One primary action per email. Make the button impossible to miss.
- **Minimal text.** The email body handles the message — the graphic is just the visual hook.
- **Brand-heavy.** Logo, brand colors, consistent header across all emails. Recognition matters in an inbox.

### Email Header Template

```jsx
<section className="flex items-center justify-between px-8 py-6 bg-gray-900 w-full h-full">
  <div className="flex items-center gap-3">
    <div className="w-8 h-8 bg-blue-500 rounded-lg"></div>
    <span className="text-xl font-bold text-white">Acme Weekly</span>
  </div>
  <span className="text-lg text-gray-400">March 2026</span>
</section>
```

### Newsletter Hero Template

```jsx
<section className="flex flex-col items-center justify-center gap-6 px-12 py-12 bg-blue-600 w-full h-full">
  <h1 className="text-4xl font-extrabold text-white text-center leading-tight">What we shipped this week</h1>
  <p className="text-xl text-blue-100 text-center">3 features, 2 fixes, 1 big announcement</p>
  <button className="px-8 py-3 text-lg font-bold text-blue-600 bg-white rounded-xl">Read the update</button>
</section>
```

---

## Blog & Article Hero Images

### Artboard Sizes

| Format | Width | Height | Use Case |
|--------|-------|--------|----------|
| **Blog Hero (16:9)** | 1200 | 675 | Standard blog header |
| **Blog Hero (2:1)** | 1200 | 600 | Wide blog header |
| **Medium Article** | 1400 | 788 | Medium.com header |
| **Substack** | 1456 | 816 | Substack header image |

### Blog Hero Tips

- Blog heroes set the mood, not deliver information. They can be more abstract and atmospheric.
- Title text on the hero is optional — many blogs render the title in HTML below the image.
- If including text: `text-5xl` to `text-6xl`, centered, with plenty of breathing room.
- Solid color backgrounds with minimal text work well for a clean, modern look.
- Use image fills (`set_fill` with type "image") for photographic heroes.

### Blog Hero Template

```jsx
<section className="flex flex-col items-center justify-center gap-6 px-16 py-16 bg-gray-900 w-full h-full">
  <span className="text-lg font-semibold text-blue-400 uppercase tracking-widest">Engineering</span>
  <h1 className="text-5xl font-extrabold text-white text-center leading-tight max-w-[900px]">How we reduced build times by 80%</h1>
  <p className="text-xl text-gray-500 font-medium">March 2026 / 8 min read</p>
</section>
```

---

## OG Images & Share Cards

### Artboard Sizes

| Format | Width | Height | Use Case |
|--------|-------|--------|----------|
| **Open Graph** | 1200 | 630 | og:image for link previews (Facebook, Discord, Slack, iMessage) |
| **Twitter Card** | 1200 | 628 | twitter:image for tweet link previews |
| **WhatsApp Preview** | 1200 | 630 | Link preview in chats |

### OG Image Best Practices

OG images appear when someone shares a URL. They render at different sizes depending on the platform (300px on Slack, 500px on Facebook, 120px in some chat apps).

1. **Title is king.** The page title or article headline should dominate the image. `text-5xl` to `text-6xl`.
2. **Brand anchor.** Logo or brand name in a corner — consistent across all OG images.
3. **No fine details.** Renders tiny on many platforms. Simple layout, high contrast, big text.
4. **Left-aligned text works well** — many platforms crop the right edge on mobile.

### OG Image Template

```jsx
<section className="flex items-center gap-12 px-16 py-12 bg-white w-full h-full">
  <div className="flex flex-col gap-4 grow">
    <div className="flex items-center gap-2">
      <div className="w-8 h-8 bg-blue-600 rounded-lg"></div>
      <span className="text-lg font-bold text-gray-400">acme.com</span>
    </div>
    <h1 className="text-5xl font-extrabold text-gray-900 leading-tight">How we reduced build times by 80%</h1>
    <p className="text-xl text-gray-500">A deep dive into our new CI/CD pipeline.</p>
  </div>
  <div className="w-[250px] h-[250px] bg-blue-100 rounded-3xl"></div>
</section>
```

---

## Certificates & Awards

### Artboard Sizes

| Format | Width | Height | Use Case |
|--------|-------|--------|----------|
| **Landscape Certificate** | 1400 | 1000 | Course completion, awards |
| **A4 Landscape** | 1123 | 794 | Printable certificate |

### Certificate Template

```jsx
<section className="flex flex-col items-center justify-center gap-8 p-24 bg-white w-full h-full border-8 border-gray-200">
  <div className="flex items-center gap-3">
    <div className="w-10 h-10 bg-blue-600 rounded-lg"></div>
    <span className="text-2xl font-bold text-gray-900">Acme Academy</span>
  </div>
  <span className="text-xl text-gray-400 uppercase tracking-widest font-semibold">Certificate of Completion</span>
  <h1 className="text-5xl font-extrabold text-gray-900 text-center">Advanced Design Systems</h1>
  <p className="text-2xl text-gray-500 text-center">Awarded to</p>
  <p className="text-4xl font-bold text-blue-600">Jane Smith</p>
  <div className="flex items-center gap-12">
    <div className="flex flex-col items-center gap-1">
      <span className="text-lg text-gray-400">Date</span>
      <span className="text-lg font-semibold text-gray-700">March 6, 2026</span>
    </div>
    <div className="flex flex-col items-center gap-1">
      <span className="text-lg text-gray-400">Instructor</span>
      <span className="text-lg font-semibold text-gray-700">Pablo Stanley</span>
    </div>
  </div>
</section>
```

---

## Business Cards

The most constrained and craft-dependent format in graphic design. Every pixel matters.

### Core Principles

- **Restraint is everything.** A business card has ~30 cm² to work with. Every element must earn its place.
- **Hierarchy through scale contrast.** Name LARGE, title medium, contact details small. Three levels, max.
- **Whitespace is luxury.** The most memorable cards are the ones with the most breathing room. Resist filling space.
- **One signature element.** A bold color block, an oversized initial, a geometric accent — pick ONE.

### Artboard Setup

- Standard: 336x192 (3.5" x 2" at 96 DPI) or 672x384 for higher fidelity
- Two artboards for front/back: "Front" and "Back"
- Front: name + title + brand. Back: contact details + address.
- Position side-by-side: x=0 for front, x=width+60 for back

### Front Layout Patterns

- **Classic**: Logo top-left, name center or left-aligned, title below name. Clean, professional.
- **Bold**: Name in text-3xl or text-4xl spanning most of the card. Title in text-xs uppercase tracking-widest.
- **Minimal**: Just the name, centered, with generous margins. Nothing else on front.
- **Asymmetric**: Name left-aligned, large color block or accent shape on right third.
- **Branded**: Full-bleed brand color with reversed (white) text.

### Back Layout Patterns

- **Info stack**: Left-aligned column: phone, email, website, address. Small icons optional.
- **Two-column**: Contact left, address right.
- **Centered**: All info centered, separated by subtle dividers or spacing.

### Typography

- Name: text-xl to text-3xl, font-bold or font-extrabold. This is the HERO of the card.
- Title/role: text-xs to text-sm, font-medium, text-muted-foreground. Quiet, subordinate.
- Contact: text-xs, font-normal. Legible but small. Use tracking-wide for all-caps labels.
- NEVER use more than 2 font weights on a business card. Bold + normal. That's it.

### Color Rules

- TWO colors max (plus black/white). Brand color + neutral.
- Full-bleed color backgrounds make cards stand out physically.
- Dark cards (bg-zinc-950, bg-slate-900) feel premium.

### Rules

- NO paragraphs. No sentences. Just name, title, contact data.
- NO decorative borders around the entire card — they feel dated.
- ALWAYS include: name, title/role, at least email and phone.
- Horizontal orientation is standard. Vertical is bold and distinctive — suggest it for creative professionals.

---

## Resumes / CVs

A single-page professional document that must be scannable in 6 seconds.

### Core Principles

- **Scannable in seconds.** Recruiters spend 6-10 seconds per resume. Visual hierarchy is critical.
- **Information-dense but organized.** Resumes pack a lot of content. The design's job is to make density feel clear, not cluttered.
- **Conservative elegance.** Bold design choices should be subtle — a strong color accent, a distinctive layout — not experimental.
- **Content is king.** The text matters more than the styling. Never sacrifice information space for visual decoration.

### Artboard Setup

- US Letter: 816x1056 (8.5" x 11" at 96 DPI)
- A4: 794x1123 (210mm x 297mm at 96 DPI)
- Single artboard — resumes should be ONE page.

### Layout Patterns

- **Two-column (most popular)**: Narrow sidebar (w-1/3) for contact, skills, education + wide main column (w-2/3) for experience, projects.
- **Single-column**: Full-width, traditional. Header -> Summary -> Experience -> Education -> Skills. Classic and ATS-friendly.
- **Top header + single column**: Bold header band with name + contact, then single-column content below.
- **Asymmetric**: Sidebar with accent color background for visual distinction.

### Section Hierarchy

1. **Name + contact** — text-2xl to text-3xl font-bold, followed by email, phone, location, LinkedIn in text-xs
2. **Experience** — company + role + dates, with 2-3 bullet achievements each
3. **Education** — degree, school, year
4. **Skills** — grouped by category (Languages, Frameworks, Tools)
5. Optional: Summary (2 lines max), Projects, Certifications

### Typography

- Name: text-2xl to text-3xl, font-bold. The only large text on the page.
- Section headers: text-sm uppercase tracking-widest font-bold — acts as visual anchors
- Company/role: text-sm font-semibold for company, font-medium for role
- Body/bullets: text-xs to text-sm, font-normal. Compact but readable.
- Dates: text-xs, text-muted-foreground, right-aligned or inline

### Color Strategy

- ONE accent color for section headers, sidebar, or name. Subtle — not a rainbow.
- Dark: slate-900 or zinc-900 for body text. Never pure black.
- Accent choices: blue-700 (professional), emerald-700 (creative), slate-700 (conservative), amber-700 (warm)
- Sidebar background: muted accent like slate-50, blue-50, or a subtle tint

### Rules

- ONE page. No exceptions unless explicitly asked for multi-page CV.
- NO photos unless explicitly asked (photos on resumes are culturally variable).
- NO icons for contact info — they waste space. Just text.
- NO decorative elements. Every pixel should convey information.
- USE consistent date formatting: "Jan 2023 - Present" or "2023 - Present"
- USE real-sounding content, not "Lorem ipsum." Realistic job titles, companies, and bullet points.

---

## Flyers (Detailed Design Guide)

Extends the "Event Posters & Flyers" section above with deeper layout patterns, color psychology, and typographic rules for standalone promotional flyers.


### Core Principles

- **Impact from 3 feet away.** Flyers compete for attention on bulletin boards, in stacks, on screens. The headline must PUNCH.
- **Essential info only.** WHAT + WHEN + WHERE + HOW (to attend/buy/register). Everything else is noise.
- **Bold visual hierarchy.** Three tiers: (1) Headline/event name — massive, (2) Key details — medium, (3) Fine print — small.
- **Energetic composition.** Flyers should feel dynamic — angled text, color blocks, overlapping elements, strong contrast.

### Artboard Setup

- Standard flyer: 816x1056 (US Letter) or 794x1123 (A4)
- Half-page: 816x528 or compact 540x720
- Social/digital flyer: 1080x1080 (square) or 1080x1350 (4:5)

### Layout Patterns

- **Stacked bold**: Giant headline top, image/graphic middle, details bottom. Reads top-to-bottom.
- **Full-bleed image + overlay**: Photo fills the card, semi-transparent overlay for text readability. Dramatic.
- **Split composition**: Color block top (headline) + white/dark bottom (details). Clean separation.
- **Diagonal energy**: Angled divider between image and text zones. Feels active and dynamic.
- **Centered stack**: Everything centered, stacked vertically. Works for elegant/minimal events.
- **Typographic**: No image — pure type. Giant headline, details in contrasting weight/size. Bold and editorial.

### Typography

- Headline: text-5xl to text-9xl, font-extrabold or font-black, tracking-tight. ALL CAPS is encouraged.
- Date: text-xl to text-2xl, font-bold. Consider uppercase tracking-widest for emphasis.
- Details: text-sm to text-base, font-normal. Quiet but readable.
- MAX 2 text sizes for details (date + location should be similar). Headline is the outlier.

### Color Psychology for Events

- **Concert/party**: Neon on black (emerald-400, fuchsia-500, cyan-400 on zinc-950). High energy.
- **Conference/professional**: Dark blue or slate on white. Clean authority.
- **Community/cultural**: Warm tones (amber, orange, rose) on cream or dark backgrounds.
- **Sale/promotion**: Red + yellow/white. Urgency colors. bg-red-600 text-white.
- **Art/gallery**: Monochromatic or black + one accent. Minimalist sophistication.

### Rules

- NO tiny text. If the date isn't readable at arm's length, it's too small.
- NO walls of text. A flyer is NOT a brochure. Max 3-4 short text blocks.
- ALWAYS include date, time, location, and how to attend. Missing info = useless flyer.
- CONTRAST is mandatory. Text must be instantly readable over any background.

---

## Restaurant / Cafe Menus

A functional document that must be beautiful, scannable, and make food sound irresistible.

### Core Principles

- **Scannability first.** Customers scan menus in an F-pattern or Z-pattern. Category headers must be instantly visible.
- **Make food the hero.** The item names and descriptions should dominate — not the decoration.
- **Price alignment.** Prices must be easy to find but not dominant. Right-aligned, consistent formatting.
- **Atmosphere through design.** The menu IS the restaurant's brand. Its style should match the dining experience.

### Artboard Setup

- Single page menu: 816x1056 (letter) or 794x1123 (A4)
- Folded menu (each panel): 408x528 or similar half-page
- Compact/table tent: 540x720 or 480x640
- For multi-page: create multiple artboards side-by-side

### Layout Patterns

- **Single column**: Category by category, top to bottom. Best for short menus (< 20 items).
- **Two column**: Left column + right column. Maximizes items per page. Use for larger menus.
- **Centered elegant**: Everything centered, generous spacing. Perfect for fine dining with fewer items.
- **Grid cards**: Items in card-style blocks with image placeholders. Modern, casual restaurants.
- **Asymmetric**: One wide feature column + one narrow specials/drinks column.

### Section Structure

```
STARTERS          <- Category header: text-lg to text-xl, font-bold, uppercase tracking-widest

Bruschetta ............... $12    <- Item name: text-sm font-semibold, price right-aligned
  Tomato, basil, balsamic         <- Description: text-xs text-muted-foreground

Burrata Salad ............ $16
  Heirloom tomato, arugula, olive oil
```

### Typography

- Restaurant name: text-3xl to text-5xl, distinctive. This sets the tone.
- Category headers: text-lg to text-xl, font-bold, uppercase, tracking-wide. Clear dividers.
- Item names: text-sm to text-base, font-semibold. The star of each line.
- Descriptions: text-xs to text-sm, font-normal, text-muted-foreground. Appetizing but compact.
- Prices: text-sm, font-medium, right-aligned or after dot leaders.

### Style by Restaurant Type

- **Fine dining**: Minimal, serif-feeling (use font-light + tracking-wide). Lots of whitespace. Dark bg or ivory.
- **Casual/bistro**: Warm tones, slightly playful. Rounded corners on sections. bg-amber-50 or bg-stone-50.
- **Modern/trendy**: Clean sans-serif, dark theme (bg-zinc-950), accent color for specials. Minimal ornament.
- **Cafe**: Light, airy. bg-white or bg-stone-50. Small illustrations encouraged.
- **Bar/cocktail**: Dark and moody. bg-zinc-950 or bg-slate-900. Gold or amber accents. Atmospheric.

### Rules

- NO prices hidden or hard to find. Price transparency builds trust.
- NO more than 7-9 items per category. Too many choices paralyze diners.
- ALWAYS include: restaurant name, categories, item names, descriptions, prices.
- USE appetizing language: "house-made", "slow-roasted", "seasonal", "fresh" — not "good" or "tasty."
- Dietary markers (V, VG, GF) should be subtle: text-xs, after the item name, muted color.

---

## Infographics / Data Visualization

A visual narrative that makes data compelling, memorable, and shareable.

### Core Principles

- **Story first, data second.** An infographic tells a story. The data supports it. Lead with the narrative arc, not the raw numbers.
- **One key insight.** Every infographic should have ONE takeaway the viewer remembers. Everything else supports that insight.
- **Visual encoding.** Numbers become shapes, sizes, colors, positions. Never just text + numbers — translate data into visual patterns.
- **Top-to-bottom flow.** Infographics read like a scroll. Clear sections with clear transitions.

### Artboard Setup

- Standard: 800x2400 to 1080x3200 (tall, scrollable)
- Social-friendly: 1080x1920 (9:16) or 1080x1350 (4:5)
- Presentation: 1920x1080 (landscape, one key chart)
- Use one tall artboard, NOT multiple. Infographics are single continuous pieces.

### Section Flow

1. **Title banner** — Bold headline that frames the story. "The State of...", "How X Changed Y", "By the Numbers: Z"
2. **Key stat** — One massive number (text-7xl to text-9xl) that hooks attention. "73% of..." or "$4.2 Trillion"
3. **Context section** — 2-3 sentences explaining why this matters. text-base, max-w-xl.
4. **Data sections** (2-4) — Each visualizing one aspect of the data. Different visual encoding per section.
5. **Comparison** — Side-by-side or before/after. Shows contrast clearly.
6. **Conclusion/CTA** — Key takeaway restated, source credits.

### Data Visualization Patterns (Using Efecto Nodes)

Since Efecto doesn't have native chart components, represent data visually with layout:

- **Bar chart**: Horizontal frames with width proportional to value. Label left, bar right.
- **Stat cards**: Large number (text-6xl font-bold) + label (text-xs uppercase). Grid of 3-4.
- **Comparison columns**: Two columns side-by-side, each with stacked items. Color-coded.
- **Timeline**: Vertical line (w-0.5 bg-border) with alternating left/right content nodes.
- **Icon array**: Grid of small icons (w-4 h-4) where colored vs muted represent proportions.
- **Ranked list**: Numbered items with bars showing magnitude. 1-5 items.

### Typography

- Title: text-3xl to text-5xl, font-extrabold, tracking-tight. Sets the topic.
- Key stat: text-7xl to text-9xl, font-black. The eye-catcher.
- Section headers: text-xl font-bold. Separates data sections.
- Labels: text-xs uppercase tracking-widest font-medium. Data annotations.
- Body text: text-sm, max 2-3 lines per section. Support, not bulk.
- Source: text-xs text-muted-foreground, bottom of infographic.

### Color Strategy

- **One primary data color** + neutral: blue-600 bars on white, emerald-500 bars on slate-50.
- **Sequential**: Light to dark shades of one color for ranked data (blue-200 → blue-800).
- **Diverging**: Two colors for comparison (emerald-500 vs rose-500, "good" vs "bad").
- **Categorical**: 3-4 distinct colors for different categories (blue, amber, emerald, rose).
- Background: Very subtle tint (slate-50, stone-50, zinc-50) or white. Never dark for full infographic.

### Rules

- NO more than 4 data sections. After 4, attention drops sharply.
- NO raw data tables. Translate EVERY number into a visual element.
- ALWAYS cite sources (text-xs at bottom). Even "Source: Company Annual Report 2025" adds credibility.
- USE real-sounding data. "73% of remote workers prefer async communication" — not "X% of people do Y."
- NEVER crowd sections. Gap-8 to gap-12 between data blocks. Let each section breathe.

---

## Invitations / Announcement Cards

A personal, often emotional piece that sets the tone for an event.

### Core Principles

- **Emotion first.** Invitations carry feeling — celebration, elegance, excitement, warmth. The design must evoke the event's spirit.
- **Essential details, beautifully arranged.** WHO is hosting, WHAT the event is, WHEN, WHERE, and HOW to respond. Presented with grace.
- **Memorability.** One unexpected element — an oversized letter, a striking color, an elegant border — makes it stick.
- **Consistency with the event.** A black-tie gala invitation looks nothing like a kid's birthday party invite. Match the formality level exactly.

### Artboard Setup

- Standard card: 672x480 (7" x 5") or 480x672 (5" x 7" portrait)
- Square: 540x540 or 648x648
- A5 landscape: 794x559
- Create TWO artboards: "Front" and "Back" (or "Details") for complete invitations

### Layout Patterns

- **Centered classic**: Everything centered, generous margins. Name/event large, details smaller below. Timeless.
- **Frame/border**: Thin decorative border (border-2 rounded-xl with p-8 to p-12 inner padding). Content centered within.
- **Split**: Left half is a color block or image, right half is text. Modern and structured.
- **Top-heavy**: Large decorative header (color block, pattern, or image) with details in lower third.
- **Minimal**: Just text on a rich background color. Extreme restraint. Works for formal events.

### Content Hierarchy

1. **Host line** (optional) — "Together with their families" or "You're invited to" — text-xs to text-sm, font-light
2. **Event/name** — THE centerpiece. "Sarah & James", "Annual Gala", "You're Turning 5!" — text-3xl to text-5xl
3. **Event type** — "Wedding Celebration", "Birthday Party", "Grand Opening" — text-sm to text-base
4. **Date + time** — Prominent. text-lg font-semibold. Consider ornamental formatting.
5. **Venue** — Name + address. text-sm to text-base.
6. **RSVP** — How to respond. text-xs to text-sm. "RSVP by March 15 to sarah@email.com"
7. **Additional info** — Dress code, parking, dietary notes. text-xs text-muted-foreground.

### Style by Event Type

- **Wedding**: Elegant, serif-feeling. Soft colors (rose-50, stone-50, amber-50). Tracking-wide for names. Minimal ornament.
- **Birthday (adult)**: Can be bold or elegant. Dark backgrounds (zinc-900 + gold accent) or bright (fuchsia, amber).
- **Birthday (kids)**: Bright, playful. Bold colors (yellow-400, sky-400, rose-400). Large, fun text. Rounded everything.
- **Corporate**: Clean, professional. Brand colors + white. Clear hierarchy. No playfulness.
- **Holiday**: Themed colors (red+green for Christmas, orange+black for Halloween). Warm and festive.
- **Baby shower**: Soft pastels (sky-100, rose-100, mint). Gentle, delicate. Lots of whitespace.

### Rules

- NO clutter. An invitation should feel spacious and intentional. When in doubt, remove elements.
- NO lorem ipsum. Use realistic names ("Emma & Lucas"), real dates, real venues.
- ALWAYS include: event name, date, time, location, RSVP method.
- For weddings: names of the couple are THE design, not a detail. Make them magnificent.

---

## Email Newsletters (Full Layout Guide)

Extends the "Email Headers & Newsletter Graphics" section above — this covers full newsletter layout design, not just header/hero graphics.

### Core Principles

- **Single column is king.** Email clients are unpredictable. Single column (max-w-xl to max-w-2xl) works everywhere.
- **Scannable blocks.** Readers skim newsletters. Each content block should be self-contained: image/headline/excerpt/link.
- **Clear visual rhythm.** Consistent spacing between sections. Repeating patterns readers can predict.
- **One CTA per section.** Each content block should have exactly one action — "Read more", "Watch", "Register."

### Artboard Setup

- Standard: 600x900 to 640x1200 (email width is typically 600px)
- Use 640 width as max — email clients clip wider layouts
- Single artboard, tall. Newsletter scrolls vertically.

### Layout Patterns

- **Header → Hero → Cards → CTA → Footer**: Most common newsletter structure.
- **Blog digest**: Repeating cards (image left, text right, or stacked) for 3-5 articles.
- **Single story**: One large featured article with supporting images and pull quotes.
- **Curated links**: Numbered list of links with brief descriptions. Minimal imagery.
- **Mixed**: Feature story + quick links + event promo + footer.

### Section Structure

```
HEADER — Logo left, date/issue right. bg-background, py-4 px-6. Thin bottom border.

HERO — Featured story. Large image (rounded-xl), headline below (text-xl font-bold),
  2-line excerpt, "Read more" link.

CONTENT CARDS (x3-5) — Repeating pattern:
  Image thumbnail (w-24 h-24 rounded-lg) left + headline (text-base font-semibold)
  + excerpt (text-sm) + link

DIVIDER — Simple horizontal rule (h-px bg-border) between sections.

CTA SECTION — "Subscribe", "Share", or event promo. bg-muted rounded-xl p-6, centered.

FOOTER — Unsubscribe link, company address, social icons. text-xs text-muted-foreground.
```

### Typography

- Header: Logo text or image. text-lg font-bold.
- Headlines: text-lg to text-xl, font-bold. Clickable-feeling.
- Body/excerpts: text-sm, font-normal. 2-3 lines max per card.
- Links: text-sm font-medium text-primary. Underlined or obvious color change.
- Section labels: text-xs uppercase tracking-widest font-semibold text-muted-foreground.
- Footer: text-xs, text-muted-foreground.

### Rules

- MAX width 640px. Never wider.
- NO complex layouts. Two columns MAX, and prefer single column.
- ALWAYS include: header with branding, content sections, unsubscribe link, footer.
- EVERY content card needs a clear action link. Don't make readers guess where to click.

---

## Business Documents / Reports

Professional business documents — structured, authoritative, and information-rich.

### Core Principles

- **Credibility through structure.** A well-organized document builds trust before the reader processes content.
- **Dense but navigable.** Business documents carry substantial text. The design's job is wayfinding.
- **Professional restraint.** No flashy design. Clean typography, consistent spacing, subtle accents.
- **Hierarchy is everything.** Title → Section → Subsection → Body → Caption. Five distinct visual levels.

### Artboard Setup

- US Letter: 816x1056
- A4: 794x1123
- For multi-page documents: multiple artboards, same dimensions, positioned vertically
- Name pages: "01 — Cover", "02 — Executive Summary", "03 — Analysis"

### Document Types

**Report / White Paper:**
- Cover page: Title (text-4xl font-bold), subtitle, author, date, company logo
- TOC (optional): Section numbers + titles + page refs
- Sections: Large headers (text-2xl font-bold) + body text (text-sm) + call-out boxes

**Proposal:**
- Cover: Project name + client name + date. Professional and tailored.
- Problem → Solution → Approach → Timeline → Pricing → Terms

**Brochure:**
- Two or three column layout
- Feature boxes: bg-muted rounded-xl p-6 with icon + heading + description
- Back page: contact info, location, website

**One-pager / Fact Sheet:**
- Everything on ONE page. Maximum information density.
- Header band with title + logo, 2-3 column body with stat boxes

### Typography

- Document title: text-3xl to text-4xl, font-bold. Cover page hero.
- Section headers: text-xl to text-2xl, font-bold. Clear section breaks.
- Subsection headers: text-base to text-lg, font-semibold.
- Body text: text-sm, font-normal, leading-relaxed. Readable paragraphs.
- Captions: text-xs, text-muted-foreground, italic. Under figures/tables.

### Structure Elements

- **Pull quote**: bg-muted p-6 rounded-xl, text-lg font-medium, border-l-4 border-primary
- **Stat box**: bg-primary text-primary-foreground p-4 rounded-xl, large number + label
- **Table**: Full-width, header row bg-muted, alternating row stripes, text-xs to text-sm
- **Dividers**: h-px bg-border between major sections

### Rules

- BODY TEXT must be text-sm with leading-relaxed. Tight leading is exhausting.
- MAX 2 columns for body text. Single column is safest for long-form reading.
- ALWAYS include: title, date, author/company, section headers, page structure.
- FIGURES and TABLES get captions: "Figure 1: Monthly active users, Jan-Dec 2025"
- NO decorative elements. Every visual element should convey information or improve navigation.

---

## General Graphic Design Tips

### Composition

- **Visual hierarchy in 3 seconds.** Squint at your design — can you tell what's most important? If not, increase the contrast between primary, secondary, and tertiary elements.
- **Rule of thirds.** Place key elements on intersection points, not dead center (unless centering is intentional for symmetry).
- **Whitespace is structure.** Padding and gaps do more work than borders and dividers. `gap-8` to `gap-16` between sections.
- **Anchor elements.** Every composition needs a visual anchor — the element your eye goes to first. Make it dramatically larger, bolder, or more colorful than everything else.

### Color for Graphics

- **3-color rule.** One dominant, one supporting, one accent. More than 3 and the design feels busy.
- **60-30-10 split.** 60% dominant (background), 30% secondary (containers, supporting elements), 10% accent (CTAs, highlights).
- **Test at 50% zoom.** If the design still reads clearly at half size, the contrast is working.

### Typography for Graphics

- **Two fonts max.** One for headlines, one for body. Same font in different weights also works.
- **Size contrast matters.** Headlines should be at least 2x the size of body text. `text-6xl` headline with `text-2xl` body, not `text-4xl` with `text-3xl`.
- **Tracking for uppercase.** Always add `tracking-wide` or `tracking-widest` to uppercase text — it looks cramped otherwise.

### Format Quick Reference

| Asset | Size | Key Rule |
|-------|------|----------|
| **Presentation** | 1920x1080 | One idea per slide, min `text-3xl` |
| **Pitch Deck** | 1920x1080 | 10 slides, big numbers, minimal text |
| **Event Poster** | 1080x1350 | What + When + Where + CTA |
| **Email Header** | 600x200 | Brand + one line, 600px max |
| **Email Hero** | 600x400 | One CTA, simple layout |
| **Blog Hero** | 1200x675 | Atmospheric, optional text |
| **OG Image** | 1200x630 | Title + brand, works at 300px |
| **Certificate** | 1400x1000 | Formal, centered, generous whitespace |
| **Business Card** | 336x192 / 672x384 | 2 colors max, 3-level hierarchy, front+back |
| **Resume** | 816x1056 | ONE page, scannable in 6 seconds, one accent |
| **Flyer** | 816x1056 | Headline PUNCHES, WHAT+WHEN+WHERE+HOW |
| **Menu** | 816x1056 | Scannable categories, prices visible, appetizing |
| **Infographic** | 1080x3200 | One key insight, visual data encoding, top-to-bottom |
| **Invitation** | 672x480 | Emotion first, match formality level exactly |
| **Newsletter** | 640x1200 | Single column, max 640px, scannable blocks |
| **Document** | 816x1056 | 5-level hierarchy, professional restraint |
