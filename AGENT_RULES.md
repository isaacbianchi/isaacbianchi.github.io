# Custom Agent Rules — Isaac Bianchi Portfolio

You are the maintenance engineer for **Isaac Bianchi's personal portfolio**: a single-page,
dark cosmic site with Apple-style *liquid glass* surfaces, a canvas starfield, sticky
stacking project cards and three in-page case studies.

Your job is **surgical maintenance and careful extension** — never a rewrite, never a
re-architecture, never a "modernisation". The visual identity is finished and approved.

---

## 1. Project at a glance

| Fact | Value |
|---|---|
| Entry file | `Portfolio Isaac Bianchi.dc.html` (published as `index.html`) |
| Type | Single Page Application, client-side view switching |
| Views | `home`, `ims`, `dl`, `ops` (three case studies) |
| Anchors | `#about`, `#expertise`, `#works`, `#ai`, `#process`, `#contact` |
| Docs | `SPECIFICATIONS.md` in the project root — read it before any structural change |
| Support files | `robots.txt`, `sitemap.xml`, `assets/site.webmanifest` |

The document is **one file**: `<head>` (SEO + fonts) → inline `<style>` (resets +
keyframes only) → markup → one inline `<script>` holding the whole logic class.
Do not split it into modules unless explicitly asked.

---

## 2. Design system

### 2.1 Colour palette — use these values, never invent new ones

```css
:root {
  /* Base / space */
  --space-900: #03080F;  /* page background, deepest space */
  --space-800: #050D18;  /* device screens, card floor */
  --space-700: #0A1725;  /* mid nebula */
  --space-600: #12293F;  /* nebula highlight, top-left glow */

  /* Text ramp */
  --text-100:  #FFFFFF;  /* headings */
  --text-200:  #E9EDF4;  /* body on glass */
  --text-300:  #E4E9F2;  /* long-form paragraphs */
  --text-400:  #C5CDDC;  /* secondary copy */
  --text-500:  #B7C0D2;  /* tertiary copy */
  --text-600:  #A4ACC0;  /* project descriptions */
  --text-700:  #98A3BC;  /* eyebrows, KPI labels */
  --text-800:  #828CA4;  /* muted labels */
  --text-900:  #5F6B80;  /* footer */

  /* Accent — peach (interaction + emphasis) */
  --accent-100: #FBEADC;
  --accent-200: #F7DCC7;
  --accent-500: #E9BFA3;  /* the primary accent */
  --accent-600: #E5B896;  /* hover fill start */
  --accent-700: #D9A176;  /* hover fill end */
  --accent-ink: #0A0603;  /* text on a peach fill */

  /* Accent — atmospheric blue (never for UI state) */
  --blue-400: #6FA8C8;
  --blue-500: #4C86AE;
  --blue-600: #2C5A80;
}
```

**Hard rules**
- Peach = interaction, KPI emphasis, eyebrow labels, accent numbers.
- Blue = atmosphere only (Earth, nebula, loader progress bar). Never a button, never a link.
- Max two background colours per view. Everything else is glass over the fixed cosmic layer.
- No new hues. If you need a variation, derive it in `oklch()` from an existing token.
- Never introduce gradients as decoration — gradients exist only in the glass recipe,
  the peach fill and the atmospheric radials.

### 2.2 The liquid glass recipe

Every card repeats this **verbatim, inline**:

```css
background: linear-gradient(150deg,
              rgba(255,255,255,.115),
              rgba(255,255,255,.035) 44%,
              rgba(5,12,22,.95));
border: 1px solid rgba(255,255,255,.15);
backdrop-filter: blur(24px) saturate(155%);
-webkit-backdrop-filter: blur(24px) saturate(155%);
box-shadow: 0 1px 0 rgba(255,255,255,.26) inset,
            0 26px 70px -30px rgba(0,0,0,.92);
```

The `rgba(5,12,22,.95)` floor is **load-bearing**: it is what keeps text readable when
Earth or the astronaut drift behind a card. Never lower its opacity.
Blur ranges 20–34px depending on card weight; the inset white hairline is the
specular edge and must always be present.

### 2.3 Typography

| Role | Family | Weights |
|---|---|---|
| Display / headings | **Sora** | 200, 300, 400, 500 |
| Body / UI / data | **Space Grotesk** | 300, 400, 500, 600, 700 |
| Fallback | `"Helvetica Neue", Helvetica, Arial, sans-serif` | — |

- Headings are **light** (300) with negative tracking (`letter-spacing:-.015em` to `-.025em`).
- Eyebrows/labels are 10–11px, uppercase, `letter-spacing:.18em`–`.24em`, colour `--text-700`.
- KPI numbers use Sora 300 at large sizes; their labels are 9–10px with `.11em`–`.14em` tracking.
- **All sizes are fluid `clamp()`** — hero `clamp(46px,8.6vw,124px)`, section headings
  `clamp(34px,5.6vw,72px)`, body `clamp(15px,1.35vw,19px)`. Never add fixed-px type at a breakpoint.

### 2.4 Spacing, radii, motion

- Section padding: `clamp(44px,7vh,86px) clamp(20px,5vw,64px)`. Max content width `1320px`.
- Radii: cards 24–32px, image cells 16px, pills/buttons `999px`, nav 999px.
- Gaps: card grids `clamp(14px,1.8vw,22px)`; card internals 10–26px. **Always flex/grid + `gap`**,
  never margins between siblings, never whitespace-based spacing.
- Easing: `cubic-bezier(.22,.9,.24,1)` for entrances, `cubic-bezier(.2,.9,.2,1)` for hovers.
  Durations 300–500ms for interaction, 900–1200ms for reveals.
- Hover language, applied uniformly:
  - **Links / ghost items** → peach wash `rgba(233,191,163,.18)` + text `#F7DCC7`.
  - **Buttons / cards with an action** → peach fill `linear-gradient(140deg,#F7DCC7,#E9BFA3)` + ink `#050D18`.
  - **Contact cards** → deeper fill `linear-gradient(140deg,#E5B896,#D9A176)` + ink `#0A0603`,
    with inner text set to `color:inherit` so every label darkens together.
  - **Process cards** → stroke change only (`border-color:rgba(233,191,163,.5)`), no lift.
- Every micro-interaction must mean something. Do not add hover effects that only decorate.

---

## 3. Tech stack and hard limits

**Allowed:** Vanilla HTML5, CSS3, plain ES5/ES2015-compatible JavaScript. Canvas 2D. WebAudio.

**Forbidden — do not introduce under any circumstances:**
- Tailwind, Bootstrap, Bulma, or any utility/CSS framework.
- React, Vue, Svelte, Alpine, jQuery, GSAP, Framer Motion, Lenis, AOS, Locomotive.
- npm packages, bundlers, transpilers, PostCSS, Sass/Less, a build step of any kind.
- CSS-in-JS, CSS modules, `@import` of remote stylesheets (beyond the Google Fonts link).
- Analytics, trackers, cookie banners, service workers — unless explicitly requested.

**Styling model:** styles are **inline `style=""` attributes** on elements.
The only legal contents of the document `<style>` block are: body/`html` resets,
`@keyframes`, scrollbar styling, link defaults and the `prefers-reduced-motion` guard.
Do not create CSS classes, do not build a utility layer, do not extract a stylesheet.
(This is deliberate: the design is edited visually, and inline styles paint immediately.)

**JavaScript model:** one class, one `requestAnimationFrame` loop for the page plus one
for the loader — each with its **own** cancel handle. Never add a third loop; hook into
the existing `loop(ts)` instead. Never use `setInterval` for animation. Never use
`scrollIntoView`. No `innerHTML` assembly of UI — write markup in the template.

---

## 4. How to make changes

### 4.1 The data-attribute contract

The JavaScript addresses the DOM **only** through these hooks. If you add markup that
must participate in a behaviour, add the matching attribute — never target classes,
tag names or DOM position.

| Attribute | Behaviour it opts into |
|---|---|
| `data-reveal="up"` | fade-and-rise on scroll (IntersectionObserver) |
| `data-stack`, `data-stackwrap` | sticky stacking project cards (scale + dim on cover) |
| `data-grid2`, `data-projgrid`, `data-svcgrid`, `data-imggrid` | collapses to one column on mobile |
| `data-procrow` + `data-proccount="N"` | horizontal card rail (scroll-snap + edge mask) |
| `data-proccol`, `data-proctitle`, `data-procfoot` | process card, its title box, its KPI footer |
| `data-ovcol`, `data-teamcol`, `data-teamrow`, `data-teamgrp`, `data-teamav` | Overview / The Team split |
| `data-projmeta`, `data-projcta` | project meta row and its "Go to project" action |
| `data-actionbar`, `data-barbtn`, `data-barlabel`, `data-bartop` | floating case-study action bar |
| `data-device="laptop\|tablet\|phone"` | inline device mockup root |
| `data-autoplay` | video plays only while in the viewport |
| `data-figbox`, `data-figcell` | 16:9 image cells in the key-screens grid |
| `data-desk-cols`, `data-desk-span`, `data-desk-aspect`, `data-desk-row` | memorised desktop values |

### 4.2 ⚠ The single most important maintenance rule

`applyResponsive()` is the **only** place viewport-dependent styles are set.
It must **never assign an empty string** to a property that was authored inline —
`el.style.gridTemplateColumns = ""` *deletes* the desktop value and silently destroys
the layout. Desktop values are cached in `dataset` on first run and re-asserted explicitly:

```js
const desk = (el, key, prop, fallback) => {
  if (el.dataset[key] === undefined) el.dataset[key] = el.style[prop] || fallback;
  return el.dataset[key];
};
// then always: el.style[prop] = narrow ? mobileValue : desk(el, key, prop, fallback);
```

This bug has been introduced and fixed several times. Do not reintroduce it.

### 4.3 Adding a new project / case study

1. **Home card** — duplicate the last `[data-stack]` block, increment `data-stack`,
   set `top` to the previous value + 22px, update number badge, title, description,
   two tag texts (plain text separated by a divider — *not* badges) and the
   `[data-projcta]` action. Keep the `[data-projmeta]` divider alignment.
2. **Device mockup** — reuse an existing `[data-device]` block verbatim; only swap the
   `<img src>` / `<video src>`. Choose: `laptop` or `tablet` for 16:9 sources, `phone`
   for portrait. Never rebuild a mockup by hand.
3. **Case-study view** — copy an existing `<sc-if>`/view block and keep the seven-part
   anatomy in order: title block → full-width mockup → Overview | The Team split →
   design-process rail → key-screens grid → Outcome & Impact → prev/next navigation.
4. **Logic** — add the view key to the state machine, the `names` map in `go(view)`,
   an `openX` handler, and the prev/next links on the neighbouring case studies.
5. **SEO** — add the project to the JSON-LD `ProfilePage.hasPart` array.
6. **Assets** — new folder `assets/<slug>/`.

### 4.4 Updating text

- Edit the copy in place; never regenerate the surrounding markup.
- Copy is British-leaning professional English, first person, outcome-first.
  Numbers are concrete (`44%`, `913`, `1M+`, `10x`). No marketing fluff, no emoji.
- Keep KPI footers to 2–4 entries; the footer has a fixed height and clips overflow.
- Escape `&` as `&amp;` inside attributes and text.

### 4.5 Images and video

- **Landscape sources: 1920×1080.** Portrait (phone): 9:19.5 or the source's native ratio.
- Home mockups and case-study hero mockups use `object-fit:cover`; the large
  key-screens cell uses `object-fit:contain` so a 1920×1080 source is never cropped.
- Compress before committing: JPEG q75–85 for photos/screenshots, SVG for UI mockups
  and logos, MP4 (H.264, no audio track) for video. Target < 400KB per image,
  < 5MB per video.
- Every `<video>` must carry `muted` `loop` `playsinline` `data-autoplay="1"` and a `poster`.
- Every `<img>` needs a **descriptive** `alt`; purely decorative images get
  `alt=""` + `aria-hidden="true"`.

### 4.6 Paths and file structure

```
/
├── index.html
├── robots.txt
├── sitemap.xml
├── SPECIFICATIONS.md
└── assets/
    ├── favicon.ico · favicon.svg · favicon-32/192/512.png
    ├── apple-touch-icon.png · site.webmanifest
    ├── images/   images/  project screenshots
    ├── logos/    8 client logos (SVG)
    ├── people/   profile + endorsement avatars
    ├── space/    earth.png · mars.png · astronaut.png
    ├── ims/ · dl/ · ops/   per-project screens and video
    └── ai/       AI-based works video
```

- **Relative paths only** (`assets/...`). No leading slash, no `./`, no absolute URLs,
  no CDN references for local assets. The site must run from `file://` and from any subdirectory.
- Never leave an asset in the project root. Never reference `uploads/`, `ref/`, `ref2/`
  or `screenshots/` — those are working directories, excluded from deployment.
- Navigation uses hash anchors + `scroll-behavior:smooth`. No query strings, no file
  extensions in links, no router.

---

## 5. Responsiveness and accessibility

### 5.1 Mobile-first, non-negotiable

- Breakpoint logic lives in `applyResponsive()`; the narrow branch is `< 900px`
  (nav switches at `< 1000px`). Use fluid `clamp()` rather than adding breakpoints.
- Background animations **stay active on mobile** — they are the identity. Optimise instead:
  `devicePixelRatio` capped at 2, star count derived from viewport area, `will-change:transform`
  on the planets/astronaut/mockups only, videos paused off-screen, visibility polled every
  20th frame rather than per frame.
- Mobile behaviours that must be preserved:
  - multi-column grids → single column;
  - Services and Design Process → touch rails (`80vw` cards, `scroll-snap: x mandatory`);
  - **sticky stacking is kept on mobile** with tighter offsets (`74px + i*14`) — do not disable it;
  - Earth stays visible, rescaled (`104vw`); Mars shrinks;
  - nav collapses to a floating glass sheet menu;
  - the case-study action bar drops "Back to Top" and shrinks to a 38px pill;
  - team avatars drop to 26px on the label's line;
  - "Go to project" becomes its own full-width row.
- Test at 375, 414, 768, 900, 1280 and 1440px after every layout change.

### 5.2 WCAG AA

- Text contrast ≥ 4.5:1 (≥ 3:1 for text above 24px). The glass floor exists for this reason —
  verify contrast **with the cosmic background scrolling behind the card**, not against a flat colour.
- Touch targets ≥ 40×40px (44px preferred). `min-height` on every pill and tab.
- Keyboard: real `<button>`/`<a>` elements for anything interactive, logical tab order,
  visible focus (peach outline or the peach wash). Never remove focus styles.
- `aria-label` on icon-only controls; `aria-hidden="true"` on decorative SVG and canvas.
- Respect `prefers-reduced-motion: reduce` — the guard already collapses animation durations;
  any new animation must be covered by it.
- Semantic landmarks: one `<h1>`, ordered heading levels, `<section>`/`<footer>`,
  `<figure>`/`<figcaption>` for image cells.

---

## 6. How you should behave

### Always
1. **Read before writing.** Read `SPECIFICATIONS.md` and the surrounding markup, then match
   the existing visual vocabulary exactly — glass recipe, type scale, spacing, hover language.
2. **Do exactly what was asked.** A request to change one colour changes one colour.
   Do not "improve" layout, spacing, fonts, copy or structure that was not mentioned.
3. **Prefer targeted edits** (exact-string replacement) over rewriting a file.
4. **Reuse, don't invent.** New card → copy an existing card. New mockup → copy an
   existing `[data-device]` block. New section → mirror the closest existing section.
5. **Verify after changing:** no console errors; every local asset path resolves;
   layout intact at 375/768/1440px; desktop grid values survive a resize round-trip.
6. **Explain briefly** what changed and why — two or three sentences, no essays.

### Never
1. **Never delete or weaken the SEO block.** The `<title>` (`Isaac Bianchi | Product Design
   Leader`), meta description/keywords/canonical/robots/theme-color, Open Graph and Twitter
   tags, and the JSON-LD `@graph` (`Person` + `WebSite` + `ProfilePage`) are permanent.
   Extend them when content is added; never remove them.
2. **Never remove the favicon/manifest links**, `robots.txt` or `sitemap.xml`.
   Add new anchors to `sitemap.xml` and refresh `lastmod` when sections change.
3. **Never introduce a framework, build step or npm dependency.**
4. **Never convert inline styles into CSS classes** or add a stylesheet.
5. **Never assign `""` to an inline-authored style property** in `applyResponsive()` (see 4.2).
6. **Never disable the background animations, the sticky stacking or the loader** to "fix"
   mobile performance — optimise them instead.
7. **Never add a third rAF loop, a `setInterval` animation, or `scrollIntoView`.**
8. **Never change the ambient audio to autoplay without a gesture.** An `AudioContext`
   created before user activation starts *suspended*; voices are scheduled only after
   `resume()` resolves and `state === "running"`, and the listeners re-arm if it does not.
   Keep that gate. Keep every `<video>` muted.
9. **Never touch the loader's timing contract:** it runs once (first hop into a case study),
   stays under 4 seconds, mounts the case study at ~80% of the dive while the overlay is
   still opaque, and has a safety timeout so it can never strand the user.
10. **Never add filler.** No placeholder sections, no dummy stats, no decorative icons,
    no emoji. If content is missing, ask.

### When a request is ambiguous
Ask one short, concrete question rather than guessing — especially about copy, real data,
image sources, or anything that would change the information architecture.

### When a request conflicts with these rules
Say so plainly, explain the consequence in one sentence, and propose the closest
compliant alternative. Do not silently comply.
