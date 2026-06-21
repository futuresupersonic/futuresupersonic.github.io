# CLAUDE.md — benjamin lemar / conceptual designer

> Session handoff document for the portfolio site build.
> Last updated: 2026-06-10
> Current state: Landing page complete. Pills moved to rounded capsules (`--radius: 45px`). `index.html` mobile nav fixed — `about` is now a real link and a circular `[+]` toggle opens the overlay (see "Mobile nav `[+]` toggle" below). `about.html` present. Case study pages in progress — `moving-bench.html` and `tfg-onomichi-retailfront.html` built; other studies exist as reference exports. `archived-identity.html` not yet built.
> **Open task:** the radius switch and the mobile `[+]` toggle fix still need to be applied to every other page — see "PENDING PATCH" at the bottom.

---

## Project overview

A conceptual designer portfolio website for **benjamin lemar** (placeholder name, to be replaced). The visual system is based on [gentle.systems](https://gentle.systems) with specific overrides. The deliverable is a static HTML/CSS/JS site, intended for deployment via Vercel or Netlify.

The current build is a **single self-contained HTML file** (`benjamin-lemar.html`) with all CSS and JS inlined. This was a deliberate choice after the user had trouble with multi-file local previews — one file, double-click, it works. When the site is split into multiple pages (about, archived-identity, case-study), CSS and JS should be extracted into shared `shared.css` and `shared.js`.

---

## File location

`benjamin-lemar.html` — root of the project. Open in any browser, no server required.

---

## Design system — exact values

These were extracted via DevTools from gentle.systems and locked in by the user. Do not change without explicit user direction.

| Token | Value |
|-------|-------|
| Background | `#f6f6f6` |
| Text color | `rgb(43, 23, 3)` (warm dark brown, NOT black) |
| Pill background | `#eeeeee` |
| Pill background hover | `#e4e4e4` |
| Accent / hover green | `#b7ff00` |
| Confidential text color | `rgba(43, 23, 3, 0.25)` |
| Font (substitute) | Instrument Sans (Google Fonts), substituting Unica77 |
| Font size | `12px` |
| Letter spacing | `-0.05px` |
| Pill height | `25px` site-wide |
| Pill horizontal padding | `5px` (content pills) / `6px 12px` (nav pills) |
| Content max-width | `1332px` |
| Outer gutter | `30px` desktop, `12px` mobile |
| Gap between content pills | `8px` desktop, `6px` mobile |
| Gap between nav pills | `11px` desktop, `8px` mobile |
| Border radius | `--radius: 45px` on all pills + nav pills (capsule shape). NOT square corners. Stored as the `--radius` token; update there. |

---

## Layout architecture

### Single shared container

Both the nav and the main content are wrapped in a `.container` div with identical `max-width`, `margin: 0 auto`, and `padding-inline`. This guarantees the left and right edges of the nav always align with the left and right edges of every content row at every viewport width. **Do not break this.**

```html
<header><div class="container"><nav>…</nav></div></header>
<main><div class="container">…content…</div></main>
```

### Fluid percentage widths

All pill widths are stored as percentages of the container, not fixed pixels. As the viewport grows, pills scale proportionally. The container caps at `1332px + 2×30px gutter`; past that, the viewport gets margin on each side.

| Pill | px @ 1332 | % |
|------|-----------|---|
| Nav: home | 426 | 31.98% |
| Nav: experiments | 271 | 20.35% |
| Nav: work | 119 | 8.93% |
| Nav: archived identity | 157 | 11.79% |
| Nav: futuresupersonic | 156 | 11.71% |
| Nav: about | 148 | 11.11% |
| CTA pill (`tap / investigate →`) | 305 | 22.90% |
| Date pill | 150 | 11.26% |
| Work title bar | 1019 | 76.50% |
| Experiments title bar | 861 | 64.64% |

These widths are stored as CSS custom properties on `:root` (e.g. `--w-cta`, `--nav-home`). Update there, not at the rule site.

---

## Tag row patterns (entry headers)

Each project entry begins with a tag row. There are three types, all summing to exactly 100% of the container width.

### Type 1 — Work entry

`title bar (76.5%) + 8px gap + CTA (22.9%)`

The title bar is a single pill containing the project name (left-aligned) and the year/status (right-aligned via `justify-content: space-between`). The CTA is the white-background `tap / investigate →` pill.

### Type 2 — Experiments / futuresupersonic entry

`date (11.26%) + gap + title bar (64.64%) + gap + CTA (22.9%)`

Date sits on the left, then the project name title bar, then the CTA.

### Type 3 — Highlight entry (e.g. Myxelium Japan)

`date (11.26%) + gap + title bar (auto, hugs image width)`

No CTA. Title bar width is determined by the image column below it. Used for "live update" / "what I'm working on now" type entries.

---

## Image rows

Below each tag row, images live in a 12-column CSS grid (`.project-row`). Column spans use `.col1` through `.col12`.

- **Default single-image work entry**: `col6` (about 663px wide on a 1440px viewport — 1/3 smaller than the previous default of col9). Use `col9` or wider only for impact entries.
- **Multi-image entries**: e.g. `col4 + col5` for a pair, `col2 + col2 + col3` for the futuresupersonic tees.
- **Image + body text variant**: when an entry has body text alongside the image, use `<div class="project-row with-text">` instead of the grid. Image goes in `<div class="image-col">` (capped at ~49.3% of container, the col6 equivalent), text goes in `<div class="body-text-col">` which is locked at 22.9% (matches CTA width) and right-aligned. Body text has 1px inner padding on each side.

### Image placeholder frames

Until real assets are added, each image slot renders as a visible frame: thin border, diagonal X across the box, and a small label naming the slot and aspect ratio (e.g. `keif-prototype-hero · 16 : 9`). Used class: `.ph-frame.ratio-vid` etc. Aspect ratios available: `ratio-land` (16:10), `ratio-port` (3:4), `ratio-sq` (1:1), `ratio-vid` (16:9).

### Replacing a placeholder with a real image

Find a slot like:

```html
<div class="ph-frame ratio-vid"><span class="ph-label">keif-prototype-hero<span class="ph-ratio">16 : 9</span></span></div>
```

Replace with:

```html
<img src="keif-prototype-hero.jpg" class="ratio-vid" style="width:100%;object-fit:cover" />
```

---

## Confidential entries

Confidential / NDA work appears as `.conf-row` blocks — strikethrough text in `rgba(43,23,3,0.25)` color. On desktop, two pills side by side: project name (flex 1) + suffix pill (305px, right-aligned, contains "confidential" or a year).

On mobile, the two pills collapse into a single combined pill: project name flush left, suffix flush right, both inside the same pill via `justify-content: space-between`. The right-side pill is hidden via `display: none` and its text content is mirrored onto the left pill as a `data-suffix` attribute (done in JS at page load), then appended visually via CSS `::after`.

---

## Hover behaviors

- **All filter nav pills** and **standard pills**: hover background changes to `#e4e4e4`, except for filter pills which go to `#b7ff00` (accent). `.no-hover` opts out.
- **CTA pill (`tap / investigate →`)**: hover changes background to `#b7ff00`; the arrow `→` fades and slides out while a filled circle `●` fades in at the same position. Pure CSS scale/opacity transition (180ms / 220ms), not an SVG morph. The user previously confirmed this is acceptable as a substitute for true SVG morphing.

---

## Mobile (≤ 720px)

### Nav

The top row shows only `home + work + about` on a single line, no wrap. The home pill flex-grows to fill available space; work and about hug their content with `padding: 6px 12px`. Inside the home pill, two spans handle the name swap:

```html
<a class="nav-pill home">
  <span class="name-full">benjamin lemar — conceptual designer</span>
  <span class="name-short">benjamin lemar</span>
</a>
```

CSS shows the full name on desktop and the short name on mobile.

The other three nav pills (`experiments`, `archived identity`, `futuresupersonic`) are hidden by default on mobile. Tapping `about` adds `.menu-open` to the header, which absolutely-positions the three hidden pills below the nav row, right-aligned, vertically stacked at fixed `top` offsets (`100% + 8px`, `+ 33px`, `+ 66px`). Each item has a 60ms staggered transition delay for a sequential reveal animation. Tapping any revealed item navigates / filters and the menu closes.

A second tap on `about` itself navigates to `about.html` (the JS handler only intercepts the first tap to open the menu).

### Content entries

- **Work entries**: CTA pill is hidden (`display: none`). The title bar becomes the clickable element via JS — clicking it reads the sibling `.pill.cta` href and navigates. CSS appends `→` to the title bar via `::after`. The title bar still uses `justify-content: space-between`, so name stays flush left and year + arrow stay flush right.
- **Experiments / futuresupersonic entries**: CTA hidden, date pill and title bar sit side by side on a single row (`flex-wrap: nowrap`). Title bar is clickable via the same JS handler, with `→` arrow appended.
- **Confidential entries**: as described above, single inline pill with `data-suffix` driven `::after`.
- **Multi-image entries**: stacked vertically, `width: 60%`, **right-aligned** (`justify-items: end`). Entries with 3+ images on desktop (only futuresupersonic tees) hide the 3rd onward via `:nth-child(n+3) { display: none }`.
- **`.with-text` entries**: image and body-text columns stack vertically at full width.

---

## JavaScript behavior

All in a single IIFE inside the file. Functions:

1. **Nav scroll hide/show** — header gets `.is-hidden` class on scroll-down (>60px), removed on scroll-up. Transitions are 250ms / 300ms.
2. **Filter pills** — clicking a `[data-filter]` nav pill hides any `.feed-entry` or `.conf-group` whose `data-cat` doesn't include the filter token. Clicking the home pill (`data-filter="all"`) shows everything. Active filter gets `.is-active` class for the green background.
3. **Mobile work/exp title-bar click** — on viewports ≤720px, clicking a `.tag-row.work .pill.title-bar` or `.tag-row.exp .pill.title-bar` reads the sibling `.pill.cta` href and navigates there.
4. **Confidential suffix mirroring** — for each `.conf-row`, copies the right pill's text content into a `data-suffix` attribute on the left pill, so mobile CSS can render it via `::after`.
5. **About mobile toggle** — first tap opens the menu reveal; second tap navigates to about.html.
6. **Copy-to-clipboard** — any element with `[data-copy]` writes that value to the clipboard on click and shows "copied" briefly.
7. **Load more** — placeholder. Decrements visible count by 30. Real implementation will fetch and append more entries.

---

## Case study pages

> Derived from the two built pages: `moving-bench.html` (embedded base64 media) and `tfg-onomichi-retailfront.html` (external `images/*` refs). Values below are the actual values in those files.

### There is no single case-study template

Each project is its own standalone HTML file, composed from a **shared shell** plus a **kit of reusable layout elements**. Two layout systems exist and **both are intentional** — the goal is not to converge them onto one template. They are different arrangements of the same underlying vocabulary, chosen to fit each project's content.

That vocabulary — the type scale, the spacing rhythm, and the card patterns — is **mimicked from the gentle.systems work samples**, the same source the landing-page tokens were extracted from. gentle.systems applies a consistent set of type, spacing, and card elements across all of its work pages; these case studies follow that lineage deliberately. When building a new case study, the move is to **compose from this kit in the gentle.systems spirit**, not to invent new type/spacing/card treatments and not to force one existing page's layout onto a project it doesn't suit.

### Shared shell — identical across every case study, canonical, do not drift

- **`<head>`**: the same `:root` tokens as `index.html` (bg, text, pill colors, `--accent #b7ff00`, `--conf`, `--content-max 1332px`, `--gutter 30px`, `--gap 8px`, `--nav-gap 11px`, pill metrics including `--radius: 45px`, easing curves, nav-pill width vars). Instrument Sans via Google Fonts. Global reset is `* { box-sizing: border-box; margin: 0; padding: 0; }` — it must NOT force `border-radius: 0`. **Note:** older builds of these pages shipped a `border-radius: 0 !important` reset from the square-corners era; if present it overrides the pill `--radius` and must be removed.
- **`<header class="header_wrapper">`**: the exact six-pill nav from index — `home` (with `.name-full` / `.name-short` swap), `experiments`, `work`, `archived`, `fss`, `about` (carries `data-mobile-toggle`). All link to `index.html` except `archived → archived-identity.html` and `about → about.html`.
- **`<footer class="footer-row">`**: email pill (`data-copy="hello@benjaminlemar.com"`) + copyright pill.
- **Back link** to `index.html` at the end of content.
- **JS** (single IIFE): scroll hide/show header (`.is-hidden` past 60px), mobile about-menu toggle (`.menu-open`), copy-to-clipboard for `[data-copy]`.
- **Mobile**: `@media (max-width: 720px)`; same nav collapse behavior as index (home + work + about visible, the other three revealed under `about`).

Wrap the content in the same `.container` the nav uses so edges align — never break this (same rule as the landing page).

### Vertical rhythm — two strategies

| Page | Wrapper | How spacing is set |
|------|---------|--------------------|
| Moving Bench | content rows live directly in `.container`, `<main class="main-content cs">` | each element carries its own bottom margin (`.cs-row` 150px, `.cs-figure` 130px, `.cs-concepts` 90px) |
| TFG Onomichi | each unit wrapped in `<div class="cs-block">`, `<main class="cs-main">` | the `.cs-block` wrapper is the rhythm unit — 90px bottom margin, `.cs-block.tight` = 56px |

### The element kit

Mix and match per project. Concrete values are from the built files.

**Title**
- `.cs-title` — project title. Moving Bench: `32px / 400 / ls -0.5px` (fixed 32px, set after two iterations — see memory). TFG: rail-indented via `margin-left: var(--cs-rail)`.

**Intro / meta**
- Moving Bench: `.cs-meta` (max-width 380px, large top/bottom margin) → `.cs-meta-k` (14px / 500 label) + `.cs-meta-v` (14px muted).
- TFG: `.cs-sublabel` → `.main` (22px text) + `.sub` (22px muted) for the tagline pair, then a `.cs-textblock` for the intro narrative.

**Section heading + body — the core editorial unit**
- Moving Bench (heading-left / text-right): `.cs-row` is a grid `1fr var(--cs-text)` where `--cs-text: 42%`. Heading in `.cs-h` (22px / 400) on the left, body in `.cs-b` (paragraphs `clamp(18px, 2.1vw, 24px)`) on the right. `.cs-b.cs-lead` styles the opening paragraph. An empty `.cs-h` left column is used when only body text is wanted.
- TFG (rail-based): `--cs-rail: 7%` (left indent for titles/labels), `--cs-col: 47%` (right text-column width). A rail-indented `.cs-label` (`.main` 22px + optional `.sub`) sits above the block; body text in `.cs-textblock` (`width: var(--cs-col)`, paragraphs `--cs-body: clamp(18px, 2.1vw, 24px)`).

**Figures**
- `.cs-figure` — full-width image or video (both systems). Variants: Moving Bench `.cs-figure.cs-sm` (70%, centered); TFG `.cs-figure.w72` (72%) and `.cs-figure.left`.
- Video figures use `<video autoplay loop muted playsinline>` with a `poster`.

**Centered statement (Moving Bench)**
- `.cs-quote` — large centered line, `clamp(22px, 3.1vw, 33px)`, 50% opacity, max-width 720px. The "What if the bench had a sense of empathy?" device — a single rhetorical beat between figures.

**Image + text split (TFG)**
- `.cs-split` — flex row of `.col-img` (`.w47` / `.w23`, inline `width` overrides are used) + `.col-text` (`var(--cs-col)`). `.col-text.dim` greys the copy; `.hl` re-emphasizes a span inside dimmed text. Used for moodboard pairs and annotated explanations.

**Card systems — two flavors, both named `.cs-card`**
- Moving Bench `.cs-tech` block: `.cs-tech-lead` (22px bold lead) + `.cs-tech-restate` (14px muted) + `.cs-cards` (vertical stack) of `.cs-card` with a **numbered** `.num` (1–4), `h3`, `p`, optional `ul`. Used for technical architecture.
- TFG `.cs-directions`: a `.rail-label` (rail-indented) + a `.cards` column (`var(--cs-col)`) of `.cs-card` with a **lettered** `.letter` (A–E), `h4`, `p`. Used for concept directions.
- **Caution:** the two `.cs-card` definitions differ (numbered vs lettered, different padding/type). They are page-scoped. If these pages ever share a stylesheet, rename one — don't let the definitions collide.

**Concept / territory grid (Moving Bench)**
- `.cs-concepts` — 4-up grid (`repeat(4, 1fr)`, 2-up on mobile) of `.cs-concept` (square `.ph` image + `h3` + `p`). Used for the "where the principle goes next" sketches.

**Pair (TFG)**
- `.cs-pair` — two `.item` blocks at 30% each, rail-indented. Photo pairs.

**Closing + back link**
- Moving Bench: `.cs-backbottom` (centered back link).
- TFG: `.cs-closing` (`var(--cs-col)` final narrative) + `.cs-back` (back link with larger padding).

### Media: known inconsistency to resolve

The project's stated principle is **self-contained, single-file portability** (media embedded as base64). `moving-bench.html` follows this. `tfg-onomichi-retailfront.html` currently references **external** `images/*.jpg` / `.gif`. Before TFG-style pages ship, their assets should be inlined as base64 to match — or the portability principle should be formally relaxed for case studies. Flag this rather than assuming; don't silently mix the two.

### Building a new case study — checklist

1. Start from the shared shell (head tokens, nav, footer, JS, back link) — copy it intact, don't regenerate it.
2. Choose/compose elements from the kit to fit the content; follow gentle.systems type/spacing/card conventions rather than inventing new ones.
3. Keep content edits **inside** the existing structure. Per memory: rebuilding an approved page from source content causes layout drift by substituting a different class architecture — only update content within the locked structure.
4. Embed media as base64 unless the portability rule has been relaxed.
5. Link the page from the matching `index.html` CTA. (Open item: the Moving Bench CTA in `index.html` still points to `href="#"` and needs updating to `moving-bench.html`.)

---

## Pages still to build

These were planned but not built. When tackled, they should reuse the shared design system. Recommended approach: split the inlined CSS and JS out of `benjamin-lemar.html` into `shared.css` and `shared.js` first, then `index.html` becomes a normal multi-file site.

### `archived-identity.html`

- Same header/footer as index
- Page header: `archived identity`
- Grid of 200×200px squares, `flex-wrap`, gap 6px, padding 30px
- Each square: solid coloured background, small label bottom-left (9px, opacity 0.35)
- Squares are placeholders for logo / identity image uploads
- Approximately 12–20 squares to start

### `about.html`

- Same header/footer as index
- Page header: `about`
- Two-column layout: left = bio paragraphs (14px, 1.75 line-height), right = structured info blocks
- Right column blocks: `Currently`, `Previously`, `Links`, `Contact`
- Each block has a small uppercase label (9px, letter-spacing 0.1em) and a list of items
- Items separated by thin border lines

### Case study pages — already underway

The original plan here was a single `case-study.html` driven by a `?id=` URL param and a JS data object. **That is not what was built.** Reality: one standalone HTML file per project (`moving-bench.html`, `tfg-onomichi-retailfront.html`, …), each composed from a shared shell + a kit of reusable elements, no data layer. See the **Case study pages** section above for the real architecture. This subsection is kept only to flag that the earlier `?id=` template idea is superseded.

---

## Deployment

1. Push to a GitHub repo
2. Connect to Vercel or Netlify (free tier)
3. Auto-deploys on every push
4. Attach custom domain via the host's DNS settings

---

## Working with this codebase — guidance for future Claude sessions

### Things the user has explicitly asked to keep

- Border radius: `--radius: 45px` site-wide — rounded capsule pills (this replaced the original square-corners decision)
- Accent color: `#b7ff00`
- Container alignment: nav and content edges always line up — never break this
- Pill height: `25px` site-wide
- Confidential text styling: greyed `rgba(43,23,3,0.25)`, strikethrough, no click action

### Process notes

- The user iterates fast and notices alignment / spacing issues quickly. When they describe a layout, double-check the math sums to 100% (or 1332px) before applying — they have caught arithmetic errors that would have shipped 9–11px short.
- The user has reverted changes once mid-session. Apply changes with `str_replace` rather than full file rewrites where possible, so reverting is also small-scoped.
- Caveats up front. The user explicitly asked for this in the first message: "If you have caveats please say them at the beginning as to not waste time or credits."
- Single-file build is the current shipping format. Don't split into multiple files unless asked.

### Things to ask before changing

- Anything that affects pill widths, gutters, or container alignment — these have been carefully tuned multiple times.
- Anything that contradicts the gentle.systems baseline (the user uses it as a reference and will check).
- Mobile vs desktop — most changes apply to one but not the other; confirm scope before applying globally.

### Don't do

- Don't set pills back to square — they use `border-radius: var(--radius)` (45px) now; don't reintroduce a `border-radius: 0` reset
- Don't use HTML `<form>` tags in artifacts (Claude artifacts limitation)
- Don't use `localStorage` / `sessionStorage` (Claude artifacts limitation)
- Don't change the warm dark brown text color to black
- Don't add emojis to the UI
- Don't reproduce song lyrics or copyrighted text content (the placeholder Kafka opening is fine, it's public domain)

---

## Changelog (this session)

In rough order:

1. Initial three-file build (`index.html`, `shared.css`, `shared.js`) with all 11 entries from the Figma reference, percentage placeholders for images.
2. Diagnosed CSS-not-loading issue → consolidated to single self-contained `benjamin-lemar.html`.
3. Added visible image placeholder frames with X marks and slot labels.
4. Locked CTA and confidential pills to 305px wide each.
5. Defined the three tag-row types (work, exp, highlight) with exact pixel math summing to 1332px.
6. All pills standardized to 25px tall, 12px font, 5px horizontal padding.
7. Nav pills sized to 426/271/119/157/156/148px summing to 1332px with 11px gaps.
8. Mobile nav: home + work + about + circular `[+]` toggle visible; `[+]` reveals overlay below. (Originally `about` doubled as the toggle — changed 2026-06-10, see below.)
9. Body text columns locked to 305px right-aligned with 1px inner padding; default image size reduced from col9 to col6.
10. Header padding standardized at 12px top, gutters at 30px desktop / 12px mobile.
11. Hero block fixed at 400px tall, 100px below nav.
12. Restructured around single `.container` class for guaranteed nav-content alignment.
13. Pill widths converted from fixed pixels to percentages for true fluid scaling.
14. Mobile nav: home pill name swap (full → "benjamin lemar"), 6px/12px padding, home flex-grows to fill.
15. Mobile work entries: CTA hidden, title bar clickable, `→` appended via `::after`.
16. Mobile experiments / futuresupersonic: date + title side-by-side single row, CTA hidden, title bar clickable.
17. Mobile confidential: collapsed into single inline pill via `justify-content: space-between`, suffix mirrored via JS to `data-suffix`, rendered via `::after`.
18. Mobile multi-image entries: vertically stacked at 60% width, right-aligned.

### 2026-06-10

19. **Corner radius reversed.** Square-corners-only retired; all pills + nav pills now use `border-radius: var(--radius)` with `--radius: 45px` (capsule shape). Doc updated throughout. `index.html` confirmed already on this system with no `border-radius:0` reset.
20. **Mobile `about` link fixed.** `about` previously carried `data-mobile-toggle` and called `e.preventDefault()` on mobile, so it could never reach `about.html` on a phone (broken on every page sharing the nav). Replaced with option 1: `about` is a plain link; a dedicated circular `[+]` pill is the menu toggle. Done in `index.html` only — other pages still pending (see PENDING PATCH).

---

## Mobile nav `[+]` toggle (architecture)

The mobile overlay (experiments, archived identity, futuresupersonic) is opened by a dedicated toggle, NOT by the `about` pill.

- HTML: `<a class="nav-pill nav-menu-toggle" href="#" data-mobile-toggle aria-label="menu" aria-expanded="false">+</a>`, placed in `.nav-row` right after `about`. `about` is a normal link (`href="about.html"`, no `data-mobile-toggle`).
- Desktop: `.nav-pill.nav-menu-toggle { display: none; }` — toggle is mobile-only.
- Mobile (`@media (max-width:720px)`): 25×25 circle (`width/height: var(--pill-h)`, `border-radius: 50%`, `padding: 0`, `font-size: 16px`), `order: 4` so it sits far right after `about`. Inherits standard `.nav-pill` background + hover.
- JS: the `[data-mobile-toggle]` handler (`const menuToggle = …`) toggles `.menu-open` on `.header_wrapper` and flips `aria-expanded`. The "close on any nav pill click" loop skips `menuToggle`. Filters bind to `[data-filter]`, which the toggle doesn't have, so it's filter-safe.
- Verified at 390px: `about` navigates, `[+]` opens/closes the overlay, no horizontal overflow.

---

## PENDING PATCH — apply to all pages except index.html

`index.html` is done. The same two-part patch must be applied to `archived-identity.html`, `about.html`, and each case-study page. These are self-contained single-file builds — **edit in place with `str_replace`, never regenerate a page** (layout drift is the #1 failure mode). Use `index.html` as the reference for exact code.

**Part A — rounded pills (every page):**
1. Ensure `:root { --radius: 45px; }`.
2. Ensure `.pill` and `.nav-pill` use `border-radius: var(--radius);`.
3. Remove any `border-radius: 0 !important` from the global reset (square-corners-era leftover) — it overrides the token and forces square pills.

**Part B — mobile `[+]` toggle (only pages using the standard nav):**
1. HTML: make `about` a plain link and add the toggle pill right after it (see architecture section above for the exact tag). `about` must NOT keep `data-mobile-toggle`.
2. Desktop CSS: `.nav-pill.nav-menu-toggle { display: none; }`.
3. Mobile CSS (inside `@media (max-width:720px)`, after the `.nav-fss` rules): the 25px circle block (see architecture section).
4. JS: copy the `menuToggle` block from `index.html` verbatim (selects `[data-mobile-toggle]`, toggles `.menu-open`, sets `aria-expanded`; close-loop skips the toggle).

**Caution:** `about.html` is a 3D iPod-style prototype and may not use the standard nav. Check first — apply Part B only if the standard nav is present; Part A still applies regardless. Verify each page at 390px (about navigates, `[+]` opens overlay, no horizontal scroll).

---

*End of handoff doc.*
