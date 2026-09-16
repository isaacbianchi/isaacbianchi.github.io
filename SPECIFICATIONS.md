# Isaac Bianchi — Portfolio · Technical Specifications

Single-page portfolio for **Isaac Bianchi**, Product Design Leader.
Dark cosmic theme, Apple-style *liquid glass* surfaces, canvas starfield, sticky stacking
project cards and three in-page case studies.

Written in **Vanilla HTML5, CSS3 and plain JavaScript**. No framework, no build step,
no npm dependencies, no lock-in — editable in any text editor.

---

## 1. File structure

```
/
├── index.html                 ← the entire site (rename from the .dc.html source)
├── robots.txt                 ← crawl rules + sitemap pointer
├── sitemap.xml                ← single URL + image sitemap
├── SPECIFICATIONS.md          ← this document
├── AGENT_RULES.md             ← maintenance rules for an AI coding agent
└── assets/
    ├── favicon.ico            favicon.svg  favicon-32.png  favicon-192.png  favicon-512.png
    ├── apple-touch-icon.png   site.webmanifest
    ├── images/                og-cover.png (1200×630 social card) + shared imagery
    ├── logos/                 8 client logos (SVG, recoloured with filter)
    ├── people/                profile photo + team and endorsement avatars
    ├── space/                 earth.png · mars.png · astronaut.png
    ├── ims/                   Next-Gen HMI Redesign — screens + prototype.mp4
    ├── dl/                    Self-Shopping Mobile App — screens + app-flow.mp4
    ├── ops/                   Production Software — screens + production.mp4
    └── ai/                    AI-based works — expert-review.mp4
```

**Paths are relative** (`assets/...`) — no leading slash, no absolute URLs. The site runs
from `file://`, from a subdirectory, or from a domain root without modification.

Working directories **excluded from the export**: `uploads/`, `ref/`, `ref2/`, `screenshots/`.

---

## 2. Colour palette

All colours are literal values in inline styles. These are the canonical tokens — if you
introduce CSS custom properties, use exactly these:

```css
:root {
  /* Base / space */
  --space-900: #03080F;  /* page background */
  --space-800: #050D18;  /* device screens, card floor */
  --space-700: #0A1725;  /* mid nebula */
  --space-600: #12293F;  /* nebula highlight */

  /* Text ramp */
  --text-100: #FFFFFF;   --text-200: #E9EDF4;   --text-300: #E4E9F2;
  --text-400: #C5CDDC;   --text-500: #B7C0D2;   --text-600: #A4ACC0;
  --text-700: #98A3BC;   --text-800: #828CA4;   --text-900: #5F6B80;

  /* Accent — peach: interaction, emphasis, KPI figures */
  --accent-100: #FBEADC; --accent-200: #F7DCC7; --accent-500: #E9BFA3;
  --accent-600: #E5B896; --accent-700: #D9A176; --accent-ink: #0A0603;

  /* Accent — atmospheric blue: Earth, nebula, loader bar. Never a UI state. */
  --blue-400: #6FA8C8;   --blue-500: #4C86AE;   --blue-600: #2C5A80;
}
```

Rules: peach for interaction and emphasis; blue for atmosphere only; at most two
background colours per view; everything else is glass over the fixed cosmic layer.

### Liquid glass recipe (repeated inline on every card)

```css
background: linear-gradient(150deg, rgba(255,255,255,.115),
                                    rgba(255,255,255,.035) 44%,
                                    rgba(5,12,22,.95));
border: 1px solid rgba(255,255,255,.15);
backdrop-filter: blur(24px) saturate(155%);
box-shadow: 0 1px 0 rgba(255,255,255,.26) inset,
            0 26px 70px -30px rgba(0,0,0,.92);
```

The `rgba(5,12,22,.95)` floor keeps text readable when Earth or the astronaut pass behind
a card — do not reduce its opacity. Standard divider: `1px solid rgba(255,255,255,.14)`.

---

## 3. Typography

| Role | Family | Weights | Notes |
|---|---|---|---|
| Display / headings | **Sora** | 200 300 400 500 | light weights, tracking −.015em…−.03em |
| Body / UI / data | **Space Grotesk** | 300 400 500 600 700 | default document font |
| Fallback | Helvetica Neue, Helvetica, Arial, sans-serif | — | |

Loaded from Google Fonts with `display=swap` and `preconnect`.
All sizes are fluid `clamp()`: hero `clamp(46px,8.6vw,124px)`, section headings
`clamp(34px,5.6vw,72px)`, body `clamp(15px,1.35vw,19px)`, eyebrows 10–11px uppercase with
`.18em`–`.24em` tracking. Numerals in aligned columns use `font-variant-numeric: tabular-nums`.

---

## 4. Spacing, radii, motion

- Section padding `clamp(44px,7vh,86px) clamp(20px,5vw,64px)`; max content width `1320px`.
- Radii: cards 24–32px, image cells 16px, pills 999px.
- Gaps via flex/grid `gap` — `clamp(14px,1.8vw,22px)` for card grids. No margin-based spacing.
- Easing `cubic-bezier(.22,.9,.24,1)` (entrances), `cubic-bezier(.2,.9,.2,1)` (hovers);
  300–500ms interaction, 900–1200ms reveals.
- Hover language: links → peach wash `rgba(233,191,163,.18)` + `#F7DCC7`; buttons → peach
  fill + ink `#050D18`; contact cards → `linear-gradient(140deg,#E5B896,#D9A176)` + `#0A0603`;
  process cards → stroke change only.

---

## 5. Document architecture

One file, four parts:

1. **`<head>`** — SEO metadata, Open Graph/Twitter, favicons, manifest, JSON-LD.
2. **Inline `<style>`** — resets, `@keyframes`, scrollbar, link defaults,
   `prefers-reduced-motion` guard. *Nothing else: no classes, no utilities.*
3. **Markup** — all styling as inline `style=""` attributes.
4. **One inline `<script>`** — a single logic class.

### Views (client-side, no router)

`home` · `ims` · `dl` · `ops`. Anchors: `#about` `#expertise` `#works` `#ai` `#process`
`#contact`, with `scroll-behavior: smooth`.

### Sections

Hero (multilingual typewriter) → About (Values / Experience tabs, orbital timeline) →
Services & Expertise (12-col grid) → Clients marquee → Selected works (sticky stacking) →
AI-based works → Design process → Endorsements → Contact → Footer.

### JavaScript components

| Component | Behaviour |
|---|---|
| Starfield | canvas 2D, parallax + twinkle, DPR capped at 2, density from viewport area |
| Planets | Earth (slow spin + per-view tween), Mars (parallax) |
| Astronaut | scroll-driven descent with sway |
| Reveal | IntersectionObserver fade-and-rise on `[data-reveal]` |
| Sticky stacking | scale + dim as the next card covers the previous |
| Loader | 3.85s interstitial, first project hop only, Earth dive + cross-dissolve |
| Ambient audio | WebAudio pad, armed on first gesture (browsers block autoplay) |
| Video autoplay | plays only while in viewport, always muted |
| Device mockups | inline laptop / tablet / phone frames, CSS only |
| `applyResponsive()` | the single source of viewport-dependent styles |

Two `requestAnimationFrame` loops only (page + loader), each with its own cancel handle.

### Data-attribute contract

JS addresses the DOM exclusively through data attributes — never classes or tag names:
`data-reveal` · `data-stack` / `data-stackwrap` · `data-grid2` / `data-projgrid` /
`data-svcgrid` / `data-imggrid` · `data-procrow` + `data-proccount` · `data-proccol` /
`data-proctitle` / `data-procfoot` · `data-ovcol` / `data-teamcol` / `data-teamrow` /
`data-teamgrp` / `data-teamav` · `data-projmeta` / `data-projcta` · `data-actionbar` /
`data-barbtn` / `data-barlabel` / `data-bartop` · `data-chalhead` / `data-chalcol` /
`data-flowrow` / `data-flowrail` / `data-flownode` / `data-resfooter` / `data-chalkpi` ·
`data-device` · `data-autoplay` · `data-figbox` / `data-figcell` ·
`data-desk-cols` / `data-desk-span` / `data-desk-aspect` / `data-desk-row`.

> ⚠ **Critical:** `applyResponsive()` must never assign an empty string to a property that
> was authored inline — `el.style.gridTemplateColumns = ""` *deletes* the desktop value.
> Desktop values are cached in `dataset` on first run and re-asserted explicitly.

---

## 6. SEO

- `<title>`: **Isaac Bianchi | Product Design Leader**
- Meta description, keywords (name-query variants), author, subject, robots
  (`max-snippet:-1, max-image-preview:large`), googlebot, bingbot, theme-color, geo.
- Canonical + `hreflang` (`en`, `x-default`) + `rel="me"` → LinkedIn.
- Open Graph (type, site_name, url, title, description, locale, `profile:first_name`/
  `last_name`) and a real **1200×630** card at `assets/images/og-cover.png`.
- Twitter `summary_large_image` with image alt.
- **JSON-LD `@graph`**: `Person` (givenName/familyName, alternateName variants, jobTitle
  list, nationality, knowsAbout, worksFor NiEW, hasOccupation, alumniOf, award, seeks,
  sameAs LinkedIn) + `WebSite` + `BreadcrumbList` (5 anchors) + `ProfilePage`
  (mainEntity, primaryImageOfPage, hasPart × 3 projects).
- One `<h1>`, ordered headings, descriptive `alt` on every content image,
  `aria-hidden` on decorative layers.
- `robots.txt` allows `/assets/` (so previews render) and points to `sitemap.xml`;
  `sitemap.xml` declares the single URL with `hreflang` and five image entries.

**After deployment:** submit the domain to Google Search Console and Bing Webmaster Tools
and request indexing — for a personal-name query this is what moves the needle fastest.
Then add the LinkedIn profile URL to the site and the site URL to the LinkedIn profile so
the two entities cross-reference.

---

## 7. Responsiveness

Mobile-first behaviour lives in `applyResponsive()`; the narrow branch is `< 900px`
(navigation switches at `< 1000px`). Fluid `clamp()` everywhere instead of breakpoints.

- Multi-column grids → single column; Services and Design Process → touch rails
  (80vw cards, `scroll-snap: x mandatory`, masked edges).
- **Sticky stacking is preserved on mobile** with tighter offsets (`74px + i*14`).
- Earth stays visible, rescaled to `104vw`; Mars shrinks.
- Navigation collapses to a floating glass sheet menu; case-study action bar drops
  "Back to Top" and shrinks to a 38px pill.
- Challenge widget: split stacks, the connector rail rotates to a vertical spine with the
  numbers in hanging indent.
- Background canvas and JS interactions stay active on mobile — optimised via DPR cap,
  area-derived star count, `will-change: transform` on moving layers, off-screen video
  pausing, and viewport visibility polled every 20th frame.
- Notch handling: `viewport-fit=cover`, `env(safe-area-inset-*)` on fixed chrome.
- `-webkit-text-size-adjust: 100%`, `overscroll-behavior-y: none`,
  `touch-action: manipulation` and a peach tap highlight on every control.
- Touch targets ≥ 40×40px (44px preferred).

---

## 8. Accessibility (WCAG AA)

Text contrast ≥ 4.5:1 (≥ 3:1 above 24px), verified with the cosmic background moving
behind the glass. Real `<button>`/`<a>` for interactive elements, logical tab order,
visible focus, `aria-label` on icon-only controls, `aria-hidden` on decorative graphics,
`prefers-reduced-motion` guard, semantic landmarks and `<figure>`/`<figcaption>`.

---

## 9. External dependencies

| Dependency | Purpose | Notes |
|---|---|---|
| Google Fonts (Sora, Space Grotesk) | typography | only remote request; self-host to go fully offline |
| — | — | no JS libraries, no CSS frameworks, no analytics, no trackers |

---

## 10. Deployment checklist

1. Rename the source file to `index.html`.
2. Remove the `<script src="./support.js">` tag and the `support.js` file
   (editor runtime only — not needed in production).
3. Replace `https://isaacbianchi.com/` with the real domain in: canonical, hreflang,
   Open Graph, Twitter, JSON-LD, `robots.txt`, `sitemap.xml`.
4. Refresh `<lastmod>` in `sitemap.xml`.
5. Upload `index.html`, `robots.txt`, `sitemap.xml` and `assets/` — exclude
   `uploads/`, `ref/`, `ref2/`, `screenshots/`, `SPECIFICATIONS.md`, `AGENT_RULES.md`.
6. Serve over HTTPS; enable gzip/brotli and long-lived cache headers on `assets/`.
7. Submit to Google Search Console and Bing Webmaster Tools; request indexing.
8. Validate: Rich Results Test (JSON-LD), Open Graph debugger, Lighthouse
   (performance / accessibility / SEO).
