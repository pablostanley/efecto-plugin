---
name: efecto-social-media
description: Design social media assets with Efecto — Instagram posts, carousels, stories, YouTube thumbnails, TikTok covers, Twitter/X images, LinkedIn slides, Pinterest pins, and Facebook graphics. Use when asked to "design a post", "create a carousel", "make a thumbnail", "design social media", or any social content task. Requires Efecto MCP server.
metadata:
  mcp-server: efecto
  author: pablostanley
  version: "1.0.0"
  argument-hint: <design-description>
---

# Efecto — Social Media Design Guide

Design social media assets — Instagram, YouTube, TikTok, Twitter/X, LinkedIn, Pinterest, Facebook — using the Efecto design tool. Proper sizing, bold typography, and platform-specific best practices.

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
create_session  label: "Instagram Carousel"
```
Returns `{ sessionId, designUrl }`. Tell the user to open the URL, then poll `session_status` until `browserConnected: true`.

### Building Social Media Designs

The primary workflow is: **create artboard → add sections with JSX → refine with updates**.

```
# 1. Create an artboard (Instagram post example)
create_artboard  name: "Slide 1"  width: 1080  height: 1080  backgroundColor: "#111827"  className: "flex flex-col"

# 2. Add content with JSX
add_section  parentId: "<artboard-id>"  jsx: '<section className="flex flex-col items-center justify-center gap-8 p-16 w-full h-full">
  <h1 className="text-7xl font-extrabold text-white text-center leading-none">Stop designing like it&apos;s 2020</h1>
  <p className="text-xl text-gray-400 font-medium">Swipe for 5 rules -></p>
</section>'

# 3. Duplicate artboard for carousel slides
duplicate_artboard  artboardId: "<artboard-id>"  newName: "Slide 2"

# 4. Read state and batch-update the duplicate
get_document
batch_update  updates: [
  { nodeId: "abc", textContent: "01" },
  { nodeId: "def", textContent: "White space is not wasted space" }
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
- **Artboard `backgroundColor` is a property, NOT a className** — use `backgroundColor: "#111827"`, not `className: "bg-gray-900"`
- **Never use arbitrary hex in className** — `bg-[#f9f9f9]` silently fails. Use named colors (`bg-gray-50`) or inline `style`
- **Use `grow` instead of `flex-1`** — `flex-1` doesn't work in Efecto
- **Buttons ignore children** — use a flex container with `<span>` + `<svg>` instead
- **Always add `w-full`** on direct children of artboards
- **For carousels**: use `duplicate_artboard` to create slides, then `batch_update` to change content per slide

---

## Images — Use Real Photos, Not Placeholders

Use `search_images` to find free, high-quality stock photos from Lummi. **No session required** — call it anytime.

```
search_images
  query: "team celebration"
  orientation: "square"
  luminance: "bright"
  limit: 4
```

Then apply images to your designs:

- **Image node**: `add_node` with `type: "image"`, `src: "<url>"`, `alt: "..."`, `className: "w-full h-full object-cover"`
- **Background fill**: `set_fill` with `fill: { type: "image", url: "<url>", size: "cover" }`
- **In JSX**: `<img src="<url>" alt="..." className="w-full h-full object-cover" />`

**Tips for social media**: Match `orientation` to the platform format — `square` for Instagram/LinkedIn posts, `vertical` for Stories/Reels/TikTok, `horizontal` for YouTube thumbnails/Twitter. Use `luminance: "dark"` for dramatic social content. Real photos make social media content feel authentic — avoid empty placeholder boxes.

---

## Shader Nodes & Visual Effects

Make social media content stand out with shader nodes. Use `add_node` with `type: "shader"`.

### Generative Backgrounds

Eye-catching backgrounds without images — perfect for quote cards, carousels, and story slides:

```
add_node
  parentId: "<artboard-id>"
  type: "shader"
  shaderType: "meshGradient"
  className: "w-full h-full"
```

**Shader types**: `meshGradient` (organic), `liquidMetal` (premium), `chrome` (bold), `pulsar` (energy), `particles` (ambient), `fireworks` (celebration).

### Apply Effects to Photos

Process images with ASCII, dither, halftone, or glitch effects for scroll-stopping visuals:

```
search_images  query: "team photo"  orientation: "square"  limit: 1
add_node
  parentId: "<artboard-id>"
  type: "shader"
  inputType: "image"
  mediaInput: { mediaUrl: "<url>", mediaType: "image", objectFit: "cover" }
  effectId: "ascii-standard"
  effectEnabled: true
  className: "w-full h-full"
```

**Best effects for social**: `ascii-standard` (tech/dev content), `dither-atkinson` (retro), `halftone-mono` (print/zine), `glitch-vhs` (nostalgic), `art-kuwahara` (painterly), `glitch-digital` (edgy).

### Post-Processes

Add film grain, vignette, or scanlines for texture:

```
postProcesses: [
  { type: "grain", enabled: true, settings: { intensity: 0.3 } },
  { type: "scanlines", enabled: true, settings: { intensity: 0.2 } }
]
```

---

## Mindset: Think Poster, Not Website

When you design a website, you're writing for someone who stopped scrolling and is reading. On social media, you have **1-2 seconds** to communicate before they scroll past. This changes everything:

- **Type big.** The canvas is 1080px wide but renders at ~375px on a phone screen. A `text-base` paragraph that's perfectly legible on a website becomes unreadable squished into an Instagram card. Default to `text-xl` or `text-2xl` where you'd normally use `text-base`.
- **Write less.** If a slide has more than 2-3 short lines, it has too much text. Cut ruthlessly. One idea per slide.
- **Think like a billboard.** Would you put a paragraph on a highway billboard? No. Same energy.

---

## Typography for Social Media

Choose font sizes based on the role, not a formula. The canvas is large (1080px+) but the content is consumed small — so sizes need to be generous.

| Role | Size | Weight | Example |
|------|------|--------|---------|
| **Hero / Impact** | `text-7xl` to `text-8xl` | `font-extrabold` or `font-black` | A single punchy statement |
| **Slide headline** | `text-4xl` to `text-5xl` | `font-bold` or `font-extrabold` | The main point of each slide |
| **Supporting text** | `text-2xl` to `text-3xl` | `font-medium` or `font-normal` | One sentence of context |
| **Labels / tags** | `text-lg` to `text-xl` | `font-semibold` | Category labels, @handles |
| **Smallest allowed** | `text-lg` | any | Nothing smaller than this. Ever. |

**Key insight:** Where you'd use `text-base` on a website, use `text-xl` on social. Where you'd use `text-2xl` for a web heading, use `text-4xl` to `text-5xl` on social. It's not scaling — it's choosing the right size for a medium that gets consumed at 1/3 the designed resolution.

---

## Copywriting: Gen-Z Designer Energy

Social media copy is NOT web copy. Write like a creative director at a streetwear brand, not a SaaS marketing team.

**Rules:**
- **Max 6-8 words per headline.** "Ship faster. Break nothing." not "Our platform helps engineering teams ship code faster while maintaining quality."
- **One idea per slide.** If you need a comma, you probably need two slides.
- **Kill filler words.** No "that", "just", "really", "very", "in order to", "leverage", "utilize".
- **Use sentence fragments.** "Built different." "Zero to launch." "Your next big thing." Complete sentences are optional.
- **Active voice, present tense.** "Build" not "Building". "Ship" not "Shipping".
- **No paragraphs.** If text wraps more than 2 lines on a social media slide, rewrite shorter.
- **CTAs are 2-3 words.** "Follow for more" / "Save this" / "Link in bio" / "Try it free"

**Bad (website brain):**
> "Our comprehensive platform provides teams with everything they need to design, build, and deploy modern web applications efficiently."

**Good (social media brain):**
> "Design. Build. Ship."

---

## Instagram

### Artboard Sizes

| Format | Width | Height | Use Case |
|--------|-------|--------|----------|
| **Post (Square)** | 1080 | 1080 | Feed posts, single images |
| **Portrait Post** | 1080 | 1350 | Feed posts (more screen real estate) |
| **Story / Reel** | 1080 | 1920 | Stories, Reels, vertical video covers |
| **Carousel Slide** | 1080 | 1080 | Each slide in a carousel |
| **Carousel (Portrait)** | 1080 | 1350 | Carousel, portrait format |

### Carousel Best Practices

1. **Slide 1 = the hook.** Bold headline, eye-catching, minimal text. This is the thumbnail in someone's feed — it decides if they stop scrolling.
2. **Consistent design system.** Same background, type styles, and layout across all slides. Only the content changes.
3. **One point per slide.** Not three. Not two. One.
4. **Number your points.** Big, bold numbers (01, 02, 03) anchor each slide and create a sense of progression.
5. **Last slide = CTA.** "Follow for more" / "Save this for later" / "Link in bio".
6. **Swipe cue on slide 1.** Arrow, dots, or "Swipe ->" so people know there's more.
7. **High contrast only.** Subtle color differences get crushed by JPEG compression and small screens.

### Carousel Template

```jsx
// Slide 1 — Hook (bold, minimal, scroll-stopping)
<section className="flex flex-col items-center justify-center gap-8 p-16 bg-gray-900 w-full h-full">
  <h1 className="text-7xl font-extrabold text-white text-center leading-none tracking-tight">Stop designing like it's 2020</h1>
  <p className="text-xl text-gray-400 font-medium">Swipe for 5 rules -></p>
</section>

// Slide 2-N — Content (one idea, big number, short text)
<section className="flex flex-col justify-center gap-8 p-16 bg-gray-900 w-full h-full">
  <span className="text-8xl font-black text-blue-400">01</span>
  <h2 className="text-4xl font-bold text-white leading-snug">White space is not wasted space</h2>
  <p className="text-2xl text-gray-400 leading-relaxed">Let your design breathe. Cramped layouts feel amateur.</p>
</section>

// Last Slide — CTA (simple, direct)
<section className="flex flex-col items-center justify-center gap-6 p-16 bg-gray-900 w-full h-full">
  <h2 className="text-5xl font-extrabold text-white text-center">Found this useful?</h2>
  <p className="text-3xl text-blue-400 font-bold text-center">Save it. Share it.</p>
  <p className="text-xl text-gray-500 font-medium">@yourbrand</p>
</section>
```

### Story Template

```jsx
<section className="flex flex-col items-center justify-center gap-10 px-12 py-24 bg-gray-900 w-full h-full">
  <span className="text-xl font-semibold text-blue-400 uppercase tracking-widest">New drop</span>
  <h1 className="text-5xl font-extrabold text-white text-center leading-tight">Your bold statement here</h1>
  <p className="text-2xl text-gray-400 text-center leading-relaxed max-w-[900px]">One short sentence of context.</p>
  <button className="px-10 py-5 text-xl font-bold text-gray-900 bg-white rounded-2xl">See more</button>
</section>
```

---

## YouTube

### Artboard Sizes

| Format | Width | Height | Use Case |
|--------|-------|--------|----------|
| **Thumbnail** | 1280 | 720 | Video thumbnail (16:9) |
| **Channel Banner** | 2560 | 1440 | Channel art (safe area: 1546x423 center) |
| **End Screen** | 1280 | 720 | End screen overlay |
| **Community Post** | 1080 | 1080 | Community tab image |

### Thumbnail Best Practices

YouTube thumbnails are the most competitive visual format on the internet. They render at ~200px wide in sidebar suggestions and ~360px in search results.

1. **Face + emotion wins.** If applicable, a human face with an exaggerated expression outperforms everything. Use large image fills for photos.
2. **Max 3-5 words.** The title does the explaining — the thumbnail does the selling. "DON'T DO THIS" beats "5 Common Mistakes Developers Make When Deploying".
3. **Massive text.** `text-6xl` to `text-8xl` `font-black`. If you can't read it at 200px wide, it's too small.
4. **High contrast outlines.** Text needs to pop against any background. Use contrasting colors or add a dark container behind light text.
5. **Split composition.** Left half = text/graphic, right half = face/subject. Or vice versa. Don't center everything.
6. **2-3 colors max.** Thumbnails with too many colors look chaotic at small sizes. Pick one bold accent color.
7. **No thin fonts.** Everything `font-bold` or heavier. Thin type disappears.

### Thumbnail Template

```jsx
<section className="flex items-center gap-8 p-12 bg-blue-600 w-full h-full">
  <div className="flex flex-col gap-4 grow">
    <h1 className="text-7xl font-black text-white leading-none uppercase">Stop doing this</h1>
    <p className="text-3xl font-bold text-blue-200">5 design mistakes</p>
  </div>
  <div className="w-[400px] h-[400px] bg-blue-800 rounded-3xl"></div>
</section>
```

### Channel Banner Template

```jsx
// Safe area is center 1546x423 — keep all important content there
<section className="flex flex-col items-center justify-center gap-4 p-16 bg-gray-900 w-full h-full">
  <h1 className="text-6xl font-extrabold text-white tracking-tight">Your Channel Name</h1>
  <p className="text-2xl text-gray-400 font-medium">Design tips every week</p>
</section>
```

---

## TikTok

### Artboard Sizes

| Format | Width | Height | Use Case |
|--------|-------|--------|----------|
| **Video Cover** | 1080 | 1920 | Video thumbnail / cover image |
| **Profile Photo** | 200 | 200 | Account avatar |

### TikTok Tips

- Vertical-first — everything is 9:16
- **Safe zones matter.** Bottom 20% is covered by captions/UI. Top 15% has the username/follow button. Keep key content in the center 65%.
- Even bolder than Instagram — TikTok moves faster
- Text must be readable over video frames — use solid color blocks or dark overlays behind text
- `text-5xl` to `text-7xl` for headlines, nothing below `text-xl`

### TikTok Cover Template

```jsx
<section className="flex flex-col items-center justify-center gap-8 px-12 py-48 bg-gray-900 w-full h-full">
  <span className="px-6 py-2 text-lg font-bold text-white bg-red-500 rounded-full uppercase">Part 3</span>
  <h1 className="text-5xl font-extrabold text-white text-center leading-tight">The trick nobody talks about</h1>
  <p className="text-2xl text-gray-400 text-center">Watch till the end</p>
</section>
```

---

## Pinterest

### Artboard Sizes

| Format | Width | Height | Use Case |
|--------|-------|--------|----------|
| **Standard Pin** | 1000 | 1500 | Standard pin (2:3 ratio) |
| **Long Pin** | 1000 | 2100 | Infographic / tall pin |
| **Square Pin** | 1000 | 1000 | Square format |
| **Board Cover** | 600 | 600 | Board thumbnail |

### Pinterest Tips

- Pinterest is a **search engine**, not a feed. Pins are discovered through keywords, not followers.
- **Tall pins win.** 2:3 ratio takes more screen space in the masonry grid. Long pins (1000x2100) for infographics or step-by-step content.
- **Text overlay is essential.** Pinterest users scan thumbnails to decide what to click. If your pin is just a photo, it gets scrolled past.
- Keep text in the top 60% — the bottom gets cropped in grid view.
- Warm, bright colors outperform dark designs on Pinterest (opposite of Instagram/YouTube).
- Include a clear CTA: "Read more", "Get the free guide", "Shop now".

### Pin Template

```jsx
<section className="flex flex-col items-center justify-between gap-8 px-12 py-16 bg-amber-50 w-full h-full">
  <div className="flex flex-col items-center gap-6">
    <span className="text-lg font-bold text-amber-700 uppercase tracking-widest">Free Guide</span>
    <h1 className="text-5xl font-extrabold text-gray-900 text-center leading-tight">10 Design Rules Every Beginner Needs</h1>
    <p className="text-2xl text-gray-600 text-center">Save this pin for later</p>
  </div>
  <div className="w-full h-[500px] bg-amber-100 rounded-3xl"></div>
  <p className="text-xl font-semibold text-amber-700">yourdomain.com</p>
</section>
```

---

## Facebook

### Artboard Sizes

| Format | Width | Height | Use Case |
|--------|-------|--------|----------|
| **Post Image** | 1200 | 630 | Shared image / link preview |
| **Cover Photo** | 1640 | 856 | Page cover (safe: center 820x312) |
| **Event Cover** | 1920 | 1005 | Event banner |
| **Story** | 1080 | 1920 | Facebook Story |
| **Ad (Square)** | 1080 | 1080 | Feed ad |
| **Ad (Landscape)** | 1200 | 628 | Feed ad, landscape |

### Facebook Tips

- Facebook compresses images aggressively — use solid colors over subtle gradients
- Link preview images (1200x630) are the most common format — they appear when sharing URLs
- Cover photos get cropped differently on mobile vs desktop. Keep critical content in the center 820x312 safe zone.
- Text-heavy images get penalized in ad delivery — keep text under 20% of the image area for ads
- Wider aspect ratios (1200x630) for feed, vertical (1080x1920) for Stories

### Post Template

```jsx
<section className="flex items-center gap-12 p-16 bg-white w-full h-full">
  <div className="flex flex-col gap-4 grow">
    <span className="text-xl font-semibold text-blue-600 uppercase tracking-wide">Announcement</span>
    <h1 className="text-5xl font-extrabold text-gray-900 leading-tight">We just launched something big</h1>
    <p className="text-2xl text-gray-500">Link in comments</p>
  </div>
  <div className="w-[350px] h-[350px] bg-blue-100 rounded-3xl"></div>
</section>
```

---

## Twitter / X

### Artboard Sizes

| Format | Width | Height | Use Case |
|--------|-------|--------|----------|
| **Image post** | 1200 | 675 | Standard tweet image (16:9) |
| **Card image** | 1200 | 628 | Link preview card |
| **Banner** | 1500 | 500 | Profile banner |
| **Two-image post** | 700 | 800 | Each image in 2-image tweet |
| **Thread graphic** | 1200 | 675 | Consistent visual for threads |

### Twitter Tips

- Even more aggressive than Instagram — the timeline is dense and fast
- Headlines: `text-5xl` to `text-7xl` `font-extrabold`
- Body: `text-2xl` to `text-3xl` max
- Aim for **one sentence** visible at a glance
- Works well with high contrast and bold color blocks
- Thread graphics: use a consistent template across all images with a numbering system (1/7, 2/7, etc.)

### Tweet Image Template

```jsx
<section className="flex flex-col items-center justify-center gap-6 p-16 bg-gray-900 w-full h-full">
  <h1 className="text-6xl font-extrabold text-white text-center leading-tight">Hot take incoming</h1>
  <p className="text-2xl text-gray-400 text-center">Your bold opinion in one sentence.</p>
  <p className="text-xl text-blue-400 font-semibold">@yourhandle</p>
</section>
```

---

## LinkedIn

### Artboard Sizes

| Format | Width | Height | Use Case |
|--------|-------|--------|----------|
| **Post** | 1200 | 1200 | Square feed post |
| **Portrait post** | 1080 | 1350 | Takes more feed space |
| **Carousel slide** | 1080 | 1080 | Document carousel |
| **Banner** | 1584 | 396 | Profile/company banner |
| **Article cover** | 1200 | 644 | Newsletter header |

### LinkedIn Tips

- Slightly more polished than Instagram, but same type-size rules apply
- Carousel slides: numbered insights, one per slide
- Include name/logo on every slide for brand recognition
- Professional does not equal boring — still use bold type and strong contrast
- LinkedIn carousels are uploaded as PDFs — each artboard becomes a page

### LinkedIn Carousel Template

```jsx
// Cover slide
<section className="flex flex-col items-center justify-center gap-8 p-16 bg-white w-full h-full border-2 border-gray-100">
  <span className="text-xl font-semibold text-blue-600">Your Name | Topic</span>
  <h1 className="text-5xl font-extrabold text-gray-900 text-center leading-tight">7 lessons from building a design system</h1>
  <p className="text-2xl text-gray-500 text-center">A thread. Slide -></p>
</section>

// Content slide
<section className="flex flex-col justify-center gap-8 p-16 bg-white w-full h-full border-2 border-gray-100">
  <span className="text-7xl font-black text-blue-600">01</span>
  <h2 className="text-4xl font-bold text-gray-900 leading-snug">Start with your constraints, not your aspirations</h2>
  <p className="text-2xl text-gray-500 leading-relaxed">A design system for 3 engineers looks nothing like one for 300.</p>
  <p className="text-lg text-gray-400 font-medium">Your Name</p>
</section>
```

---

## General Design Rules

### Layout
- **Center-align** most content (social is not web's default left-align)
- **Generous padding**: `p-12` to `p-16` minimum — don't crowd the edges
- **Vertically center**: `justify-center` on slides — content should sit in the middle, not hang from the top
- **`w-full h-full`** on top-level sections to fill the artboard completely

### Color & Contrast
- Minimum 4.5:1 contrast ratio — feed images get JPEG-compressed, low contrast disappears
- Solid backgrounds beat gradients for text readability
- Dark mode designs (white on dark) tend to pop more in feeds
- Avoid pastel-on-pastel — it looks washed out at thumbnail size

### Multi-Slide Consistency
- Same background color on every slide
- Same headline position (same y-position across slides)
- Same font sizes for the same role (all headlines match, all body text matches)
- Brand element (@handle or logo) in the same spot on every slide

### Platform Quick Reference

| Platform | Best Format | Text Style | Vibe |
|----------|-------------|------------|------|
| **Instagram** | 1080x1350 portrait | Bold, centered | Visual-first, aesthetic |
| **YouTube** | 1280x720 thumbnail | Massive, high-contrast | Clickbait energy, faces |
| **TikTok** | 1080x1920 vertical | Bold with safe zones | Fast, punchy, trending |
| **Pinterest** | 1000x1500 tall | Warm, clear overlay | Aspirational, search-driven |
| **Facebook** | 1200x630 landscape | Clean, readable | Broad audience, shareable |
| **Twitter/X** | 1200x675 landscape | One-liner, bold | Opinionated, minimal |
| **LinkedIn** | 1080x1080 square | Professional-bold | Thought leadership, clean |
