# Isaac Bianchi — Portfolio · Technical Specifications

Single-page portfolio for **Isaac Bianchi**, UI/UX & Product Design Leader.
Dark cosmic theme, Apple-style *liquid glass* surfaces, canvas starfield, sticky
stacking project cards and three in-page case studies.

- **Entry file:** `Portfolio Isaac Bianchi.dc.html` (rename to `index.html` when publishing)
- **Stack:** vanilla HTML5 + CSS3 + plain JavaScript. No build step, no framework
  lock-in, no npm dependencies. Any text editor can maintain it.
- **Rendering model:** one document, client-side view switching (`home` / `ims` / `dl` / `ops`).
  All animation runs on a single `requestAnimationFrame` loop.

---

## 1. Colour palette

Every colour below appears literally in the markup. Copy this block into a
stylesheet if you prefer CSS variables:

```css
:root {
  /* Base / space */
  --space-900: #03080F;  /* page background, deepest space */
  --space-800: #050D18;  /* device screens, card floor */
  --space-700: #0A1725;  /* mid nebula */
  --space-600: #12293F;  /* nebula highlight, top-left glow */

  /* Text */
  --text-100:  #FFFFFF;  /* headings */
  --text-200:  #E9EDF4;  /* body on glass */
  --text-300:  #E4E9F2;  /* long-form paragraphs */
  --text-400:  #C5CDDC;  /* secondary copy */
  --text-500:  #B7C0D2;  /* tertiary copy */
  --text-600:  #A4ACC0;  /* project descriptions */
  --text-700:  #98A3BC;  /* eyebrows, KPI labels */
  --text-800:  #828CA4;  /* muted labels */
  --text-900:  #5F6B80;  /* footer */

  /* Accent — peach */
  --accent-100: #FBEADC; /* gradient start */
  --accent-200: #F7DCC7; /* hover fill start */
  --accent-500: #E9BFA3; /* primary accent */

  /* Accent — atmospheric blue */
  --blue-400: #6FA8C8;
  --blue-500: #4C86AE;
  --blue-600: #2C5A80;

  /* Glass recipe (repeat verbatim on each card) */
  --glass-bg: linear-gradient(150deg,
                rgba(255,255,255,.115),
                rgba(255,255,255,.035) 44%,
                rgba(5,12,22,.95));
  --glass-border: 1px solid rgba(255,255,255,.15);
  --glass-blur: blur(24px) saturate(155%);
  --glass-shadow: 0 1px 0 rgba(255,255,255,.26) inset,
                  0 26px 70px -30px rgba(0,0,0,.92);
}
```

**Accent usage rules**
- Peach `#E9BFA3` = interaction, KPI emphasis, eyebrow labels.
- Blue family = atmosphere only (Earth, nebula, loader progress) — never for UI state.
- Hover language is uniform: links get a peach wash (`rgba(233,191,163,.18)` + `#F7DCC7` text),
  buttons get a peach fill (`linear-gradient(140deg,#F7DCC7,#E9BFA3)` + `#050D18` ink).

---

## 2. Typography

| Role | Family | Weights | Notes |
|---|---|---|---|
| Display / headings | **Sora** | 200, 300, 400, 500 | loaded from Google Fonts |
| Body / UI / data | **Space Grotesk** | 300, 400, 500, 600, 700 | loaded from Google Fonts |
| Fallback chain | `"Helvetica Neue", Helvetica, Arial, sans-serif` | — | applies if the CDN is blocked |

```html
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Sora:wght@200;300;400;500;600&family=Space+Grotesk:wght@300;400;500;600;700&display=swap">
```

All type is fluid via `clamp()` — e.g. the hero is
`clamp(46px, 8.6vw, 124px)`, section headings `clamp(34px, 5.6vw, 72px)`,
body copy `clamp(15px, 1.35vw, 19px)`. There are **no fixed breakpoint font sizes**:
resizing the viewport scales the whole scale continuously.

---

## 3. Component structure

### 3.1 Page sections (in document order)

| # | Section | Anchor | Notes |
|---|---|---|---|
| 1 | Loading interstitial | — | shown once, on the first hop into a case study |
| 2 | Cosmic background | — | fixed layer: canvas starfield, Mars, Earth, astronaut |
| 3 | Floating nav | — | glass pill, appears after 90px of scroll; ambient-sound toggle |
| 4 | Hero | `#top` | looping multilingual typewriter greeting + KPI strip |
| 5 | About me | `#about` | profile card + **My Values / My Experience** tab switch |
| 6 | Services & Expertise | `#expertise` | 4 pillars on a 12-column glass grid |
| 7 | Client marquee | — | 8 logos, CSS `@keyframes om-marquee`, 31.5s loop |
| 8 | Selected Works | `#works` | 3 sticky stacking cards with in-code device mockups |
| 9 | AI-based Works | `#ai` | 2 analytic cards, video in laptop mockups |
| 10 | Design Process & Outcomes | `#process` | 4 cards + impact metrics |
| 11 | Endorsements | — | 3 glass quote cards with real avatars |
| 12 | Contact | `#contact` | email (click-to-copy) + LinkedIn |
| 13 | Footer | — | global, present on home and every case study |
| 14 | Case studies | — | IMS / Datalogic / OPS, mounted in place of the home view |

### 3.2 Case-study anatomy (identical for all three)

1. Title block + eyebrow (+ Red Dot badge on IMS)
2. Full-width animated device mockup
3. **Overview | The Team** two-column split with a centred hairline divider
4. Design process — horizontal card rail (4–6 steps, scroll-snapped, edge-faded)
5. Key screens — 12-column image grid, 16:9 cells
6. Outcome & Impact — three headline metrics
7. Prev / next project navigation + floating action bar

### 3.3 Device mockups

Written as **inline markup**, not as an imported component, so the page has zero
runtime fetches and survives being opened from `file://`.

| Device | Screen ratio | Used for |
|---|---|---|
| `[data-device="laptop"]` | 16:9 | OPS project, both AI-based videos |
| `[data-device="tablet"]` | 16:9 | Next-Gen HMI |
| `[data-device="phone"]` | 1179:2556 (iPhone) | Self-Shopping app |

To swap a screen, change the `src` of the `<img>` or `<video>` inside the
device's screen container. Sources are expected at **1920×1080** for
laptop/tablet and portrait for phone.

### 3.4 Data attribute contract

The JavaScript never relies on classes or DOM position — only on these hooks:

| Attribute | Purpose |
|---|---|
| `data-reveal="up"` | IntersectionObserver fade-and-rise on enter |
| `data-stack` / `data-stackwrap` | sticky stacking project cards (scale + dim on cover) |
| `data-grid2`, `data-projgrid`, `data-svcgrid`, `data-imggrid` | grids that collapse to one column on mobile |
| `data-procrow` + `data-proccount` | horizontal card rail (scroll-snap + edge mask) |
| `data-proccol`, `data-proctitle`, `data-procfoot` | process card, its title box and its KPI footer |
| `data-ovcol`, `data-teamcol`, `data-teamrow`, `data-teamgrp`, `data-teamav` | Overview / The Team split |
| `data-projmeta`, `data-projcta` | project meta row and its "Go to project" action |
| `data-actionbar`, `data-barbtn`, `data-barlabel`, `data-bartop` | floating case-study action bar |
| `data-device` | device mockup root |
| `data-autoplay` | video plays only while in the viewport |
| `data-desk-cols`, `data-desk-span`, `data-desk-aspect`, `data-desk-row` | memorised desktop values, re-asserted on resize |

> **Important maintenance note:** `applyResponsive()` must never assign an empty
> string to a property that was authored inline — that deletes the desktop value.
> Desktop values are cached in `dataset` on first run and re-applied explicitly.

---

## 4. JavaScript architecture

One class drives the page. Its responsibilities, in order:

| Method | Responsibility |
|---|---|
| `componentDidMount` | starts the rAF loop, observers, typewriter, ambient-audio arming |
| `loop(ts)` | **the only rAF loop**: starfield, shooting star, click sparks, planet tween, video polling |
| `initStars` / `tweenSpace` | starfield generation; eased parallax for Earth, Mars, astronaut |
| `onScroll` | scroll progress bar, nav reveal, sticky-stack scaling, space targets |
| `applyResponsive` | the single source of truth for every viewport-dependent style |
| `setupObserver` / `revealAll` | reveal-on-scroll, with a safety pass so nothing stays hidden |
| `go(view)` | view switching; runs the interstitial only on the first project visit |
| `runLoader` | one rAF timeline: fade in → count up → dive into Earth → cross-dissolve |
| `runLoadStars` | warp-streak starfield for the interstitial (own rAF handle) |
| `startGreeting` | multilingual typewriter, writes straight to the DOM node |
| `armAmbient` / `playAmbient` | WebAudio ambient pad, gated on a user gesture |
| `playVisibleVideos` | plays/pauses `[data-autoplay]` videos and force-mutes every video |

### Autoplay-policy note (ambient audio)

An `AudioContext` created before a user gesture starts **suspended**. Scheduling an
envelope against a suspended clock means the envelope has already elapsed by the time
the context resumes — which is silence. The implementation therefore:

1. arms listeners for `pointerdown` / `keydown` / `wheel` / `touchstart`;
2. creates the context, calls `resume()`, and **only schedules the voices once
   `resume()` resolves and `ctx.state === "running"`**;
3. exposes a sound button in the nav so the visitor can retrigger or mute it.

### Performance

- Exactly **two** rAF loops exist at any time (page loop + loader loop), each with
  its own cancel handle so they can never cancel one another.
- Canvas is sized to `devicePixelRatio` capped at **2** — no 3× buffers on phones.
- Star density is area-derived (`w*h/9000`), so a phone allocates far fewer particles.
- `will-change: transform` on the planets, astronaut and device mockups only.
- Videos are paused when off-screen; visibility is polled every 20th frame, not per frame.
- `@media (prefers-reduced-motion: reduce)` collapses every animation duration.

---

## 5. Mobile-first behaviour

The narrow branch (`< 900px`) is handled in `applyResponsive()`:

- multi-column grids collapse to a single column;
- Services and Design Process become **touch rails** (`80vw` cards, `scroll-snap: x mandatory`);
- sticky stacking is **kept** on mobile with tighter offsets (`74px + i*14`);
- Earth stays visible, rescaled to `104vw`; Mars shrinks to 78px;
- section padding becomes `clamp(52px,9vh,86px) 18px`;
- nav collapses into a floating glass sheet menu;
- the case-study action bar loses "Back to Top" and shrinks to a 38px pill;
- team avatars drop to 26px and sit on the label's line;
- the "Go to project" action becomes its own full-width row;
- every touch target stays ≥ 40×40px.

---

## 6. External dependencies

| Dependency | Purpose | Removable? |
|---|---|---|
| Google Fonts (Sora, Space Grotesk) | typography | yes — fallback chain already declared |
| `support.js` | authoring-runtime for the editing environment | yes, when exporting to static hosting |

There are **no** other third-party libraries: no jQuery, no GSAP, no Tailwind, no
React in the published output. Every animation is CSS keyframes or plain rAF.

---

## 7. File structure

```
/
├── index.html                  ← rename of "Portfolio Isaac Bianchi.dc.html"
├── robots.txt
├── sitemap.xml
├── SPECIFICATIONS.md
└── assets/
    ├── favicon.ico             (32×32, PNG-in-ICO)
    ├── favicon.svg             (scalable, primary)
    ├── favicon-32.png
    ├── favicon-192.png
    ├── favicon-512.png
    ├── apple-touch-icon.png    (180×180)
    ├── site.webmanifest
    ├── images/                 project screenshots
    ├── logos/                  8 client logos (SVG)
    ├── people/                 profile and endorsement avatars
    ├── space/                  earth.png, mars.png, astronaut.png
    ├── ims/                    Next-Gen HMI screens + prototype video
    ├── dl/                     Self-Shopping screens + app-flow video
    ├── ops/                    Production Software screens + video
    └── ai/                     AI-based works videos
```

Paths are **relative** throughout (`assets/...`), so the site works from any
subdirectory and from the local filesystem.

---

## 8. SEO & sharing

- `<title>`: `Isaac Bianchi | Product Design Leader`
- meta description, keywords, author, robots, canonical, theme-color
- Open Graph (`og:type/site_name/url/title/description/image/locale`) + Twitter card
- Schema.org JSON-LD `@graph`: `Person` + `WebSite` + `ProfilePage` with the three projects
- `sitemap.xml` lists the root plus every in-page anchor
- `robots.txt` allows everything except the working directories and points at the sitemap

### Clean URLs

The site is a single-page app: navigation uses HTML anchors
(`#about`, `#expertise`, `#works`, `#ai`, `#process`, `#contact`) with
`scroll-behavior: smooth` on `html`. No file extensions or query strings appear in
the address bar. Case studies are client-side view swaps.

---

## 9. Publishing checklist

1. Rename `Portfolio Isaac Bianchi.dc.html` → `index.html`.
2. Remove the `<script src="./support.js">` tag and the `<x-dc>` / `<helmet>`
   wrappers (move the helmet's contents into `<head>`).
3. Replace `https://isaacbianchi.com/` with the real domain in the head and
   `sitemap.xml`, and refresh `lastmod`.
4. Optionally add a 1200×630 `assets/og-cover.png` and repoint `og:image` /
   `twitter:image` at it for large social previews.
5. Drop `uploads/`, `ref/`, `ref2/`, `screenshots/` from the deployment bundle.
