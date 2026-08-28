# Authoring guide — writing HTML worth sharing

Read this before generating HTML that will be published with htmltolink. The
genre is specific: **one self-contained file, opened cold by strangers, usually
on a phone, arriving from a link in a chat app**. Every choice below follows
from that.

## The `<head>` recipe

The unfurl (the preview card in WhatsApp / Slack / iMessage / Twitter) is the
page's first impression, and most generated pages skip it. Include all of this:

```html
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Trip Plan: Kyoto in 4 Days</title>
  <meta name="description" content="Day-by-day itinerary with maps and costs.">
  <meta property="og:title" content="Trip Plan: Kyoto in 4 Days">
  <meta property="og:description" content="Day-by-day itinerary with maps and costs.">
  <meta property="og:type" content="website">
  <meta name="theme-color" content="#1a1a2e">
  <link rel="icon" href="data:image/svg+xml,<svg xmlns=%22http://www.w3.org/2000/svg%22 viewBox=%220 0 100 100%22><text y=%22.9em%22 font-size=%2290%22>🗺️</text></svg>">
</head>
```

Notes:

- `<title>` names the page ("Q3 Sales Recap"), it doesn't describe it
  ("A page showing sales data"). Never "Document" or "Untitled".
- The emoji-SVG favicon is one line and replaces the generic gray globe in the
  browser tab. Pick an emoji that matches the page.
- `og:image` is optional (there's nowhere to host a separate image), but
  `og:title` + `og:description` alone already produce a proper preview card.

## Layout: mobile first, one column

Design for a ~375px-wide screen, then let it breathe on desktop:

- Wrap content in a container: `max-width: 42rem; margin: 0 auto;
  padding: 1.5rem;` (wider, ~64–72rem, for dashboards/tables).
- Base font size 16px minimum; line-height around 1.6 for body text.
- The page body must never scroll horizontally. Anything intrinsically wide —
  tables, code blocks, big diagrams — goes inside its own
  `overflow-x: auto` wrapper.
- Images: `max-width: 100%; height: auto;`.
- Tap targets (buttons, links acting as buttons) at least ~44px tall.

## Color and dark mode

Define colors once as custom properties and support both themes — a page opened
from a chat at night in light-blast white feels broken:

```css
:root {
  --bg: #ffffff; --fg: #1a1a1a; --muted: #6b7280;
  --accent: #2563eb; --surface: #f4f4f5;
}
@media (prefers-color-scheme: dark) {
  :root {
    --bg: #111114; --fg: #e7e7ea; --muted: #9ca3af;
    --accent: #60a5fa; --surface: #1c1c21;
  }
}
body { background: var(--bg); color: var(--fg); }
```

Use the tokens everywhere; never hardcode a color that only works in one theme.
A page with a deliberate single look (e.g. a dark poster) may skip the media
query but must still set background and text colors explicitly.

## Typography

The system font stack costs zero bytes and zero requests:

```css
font-family: system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
```

A Google Fonts `<link>` is fine when the page wants a distinct voice (one
family, two weights, always with a fallback stack) — but it's an external
dependency; the system stack is the safe default.

## Self-contained means self-contained

The page is published as one file with nothing next to it:

- All CSS in `<style>`, all JS in `<script>` — no `./style.css`, no `./app.js`.
- Images either from an https URL that will stay up, or embedded as `data:`
  URIs. Embedded images count toward the 10 MB cap — keep them small
  (compress, resize; prefer SVG for graphics).
- Every external URL must be `https://` — the page is served over https and
  browsers silently block `http://` sub-resources.
- Icons: inline SVG or emoji, not an icon-font CDN.

## Interactivity: client-side only

There is no backend. What works, and what to reach for:

- **State that persists per visitor** (a checklist, a filter, a draft):
  `localStorage`, with every read/write in `try/catch` and the page rendering
  correctly when storage is empty or unavailable.
- **Libraries**: load from a major CDN (cdnjs, jsdelivr), pinned to an exact
  version, script tag placed before the inline script that uses it. Don't pull
  a framework for what 30 lines of vanilla JS can do.
- **Charts/diagrams**: inline SVG, or a pinned CDN chart library.
- The page should still show its content if JS fails to load — render the
  content in HTML and use JS to enhance it, not to construct it, whenever
  practical.

**What the platform cannot do** — design around these, don't ship them broken:

- No form submissions: there is no server to receive a `POST`. If the user
  wants to "collect" something, use a `mailto:` link, a copy-to-clipboard
  button, or link to a real form service — and say which you chose.
- No multi-page navigation: it's one URL. Use in-page sections with anchor
  links, or tabs toggled by JS.
- No shared or server state: nothing one visitor does is visible to another.
  Don't build a poll or counter that pretends otherwise.
- No secrets: everything in the file is public, including anything in the JS.

## Accessibility floor

- One `<h1>`, headings in order, semantic elements (`<nav>`, `<main>`,
  `<button>` for actions — not clickable `<div>`s).
- `alt` text on meaningful images; `alt=""` on decorative ones.
- Text contrast ≥ 4.5:1 against its background in both themes.
- Visible focus states — don't `outline: none` without a replacement.

## Before you publish

Run the pre-publish checklist in SKILL.md. Then reread the page top to bottom
once as a stranger on a phone: does the first screen say what this is and why
you'd scroll?
