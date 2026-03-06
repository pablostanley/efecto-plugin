---
name: graphic-design
description: Design graphic assets — presentations, pitch decks, event posters, email headers, blog images, open graph cards, and print-style layouts with bold visual design.
---

# Efecto — Graphic Design Guide

Graphic design assets live between web pages and social media posts. They're self-contained visual pieces — a presentation slide, an event poster, a blog hero image, an email header. Each has its own constraints, but they share a common DNA: clear hierarchy, intentional composition, and designs that work at their intended viewing size.

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
4. **Full-bleed visuals.** When using an image, let it fill the entire slide. Overlay text on a dark gradient or solid block.
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
