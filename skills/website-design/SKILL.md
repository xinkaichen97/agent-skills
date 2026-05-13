---
name: website-design
description: Design and style personal portfolio websites with modern, readable aesthetics. Use when building or updating HTML/CSS or React/Next.js/TypeScript sites — covers layout, typography, color themes, animations, data modeling, and GitHub Pages deployment.
---

# Website Design

A skill for designing clean, modern personal portfolio websites. Two stacks covered: **Bootstrap 3 (static HTML/CSS)** and **Next.js + Tailwind + TypeScript (React)**. These guidelines encode what actually works.

---

## Design Thinking

Before coding, commit to an aesthetic direction:

- **Purpose**: What does this site convey? Who is the audience?
- **Tone**: Pick a direction and commit — minimal/refined, editorial, bold/typographic, warm/organic. Consistency matters more than any single choice.
- **Accent color**: Every portfolio needs one strong accent. Pair it with a near-white background and dark body text. Avoid distributing too many colors.
- **Differentiation**: What makes this site feel personal, not generic?

---

## Typography

### Go bigger than instinct — always

| Element | Minimum |
|---|---|
| Page H1 | `3rem+`, weight 700 |
| Section headings | `1.8rem+`, weight 700 |
| Body / paragraph | `1.2rem+` |
| Nav links | `1.25rem+` |
| Nav brand | `1.4rem+` |
| Metadata / dates | `1rem+` |
| Section labels | `0.7rem`, `letter-spacing: 0.2em`, uppercase |

**Font weight matters as much as size.** `weight: 300` at `4rem` reads smaller than `weight: 700` at `2.5rem`. Use 600–700 for headings unless deliberately going editorial-light.

**Bootstrap 3:** Sets `body { font-size: 14px }`. `rem` is relative to `html` (16px), not `body` — counter by setting `body { font-size: 16px }` explicitly.

**Font choice:** Inter is reliable. For something more distinctive, pair a light display font for headings with a clean body font.

---

## Color Theming

Always use CSS custom properties keyed to role, not color name:

```css
:root {
  --bg: #fafafa;
  --surface: #ffffff;
  --accent: #c8952a;          /* one strong accent */
  --accent-dim: rgba(..., 0.15);
  --text-primary: #1a1a1a;
  --text-secondary: #555555;
  --text-muted: #999999;
  --border: #e8e2db;
}
```

Name the variable `--accent`, not `--gold` or `--blue` — the role stays fixed even when the color changes. Example accents: `#c8952a` (gold), `#c8956c` (terracotta), `#5b8db8` (slate blue), `#6b9e78` (sage).

Three small details that add polish: match `::selection` background to `--accent`; set `html { scroll-behavior: smooth }`; add a 2%-opacity SVG noise texture via `body::before` (gives flat white backgrounds depth without adding visual weight).

---

## Layout & Width

**Never `max-width` a content wrapper.** Narrow columns feel cramped; fill the page. Use a generous outer container (`max-width: 1400px` with `px-6 lg:px-12`) and let inner content breathe.

### Bootstrap 3: Kill nested grid padding

Bootstrap's `.container > .row > .col-md-12` each add 15px padding. Zero them all out, then use negative margins on the inner container to expand content beyond the nav's padding — this lets you widen content without touching nav spacing:

```css
/* Nav margin lives here — don't touch */
.main.container { padding: 48px 56px; }

/* Content breaks wider without affecting nav */
.main.container > .container {
  padding: 0;
  width: calc(100% + 64px);
  margin-left: -32px;
  margin-right: -32px;
}
/* Reset on mobile */
@media (max-width: 768px) {
  .main.container > .container { width: 100%; margin: 0; }
}
```

---

## Scroll Animations

### Bootstrap 3 — WOW.js

Add `class="wow fadeInUp"` with `data-wow-duration` and `data-wow-delay` attributes. Stagger cards by incrementing delay (0.1s, 0.15s, 0.2s…).

**Critical:** Subpages (e.g. `/work/index.html`) must use absolute script paths. `./js/wow.min.js` resolves to `/work/js/wow.min.js` (doesn't exist) — always use `/js/wow.min.js`.

### React / Next.js — Framer Motion

Build a single `AnimatedSection` wrapper component that uses `useInView({ once: true, margin: "-80px" })` with directional offsets (`up`, `down`, `left`, `right`) and a `delay` prop for staggering. Wrap every page section in it.

For hero parallax: combine `useScroll` (targeting the hero ref) with `useTransform` to map scroll progress to a Y offset on the background image and an opacity fade on the content. This creates depth without JavaScript frame loops.

For animated nav pills: give the active indicator `layoutId="nav-pill"` — Framer's shared layout context handles the spring transition automatically.

For filtered lists: wrap the grid in `AnimatePresence mode="wait"` and key it to the active filter — the grid fades out/in on category change.

---

## Page Structure Patterns

**Home page:** Flowing prose, no card borders. Sections have no background, border, or box-shadow — content breathes openly.

**Work / Projects:** Bordered cards with hover lift. `border: 1px solid var(--border)`, `border-radius: 12px`, `translateY(-4px)` + `border-color: var(--accent-dim)` on hover.

**Section labels:** A tiny all-caps tracking label (`font-size: 0.7rem`, `letter-spacing: 0.2em`) above each heading creates editorial hierarchy without competing with the heading itself.

**Buttons:** Accent-outlined by default, fill accent on hover with dark (not white) text. This matters: white text on a warm accent tends to look washed out.

---

## TypeScript / Next.js Patterns

These patterns apply to any Next.js portfolio; they're drawn from real sites and reduce common structural mistakes.

### Single-source site config

Put all personal info in `src/data/siteConfig.ts`: name, title, location, email, social links, description, URL. Import it everywhere — metadata, header, footer, hero. Never hardcode strings in multiple files.

### Typed content data

Model your content (projects, work experience, timeline) as typed interfaces in `src/data/`. Include `slug`, display fields, and any union types for categories. Add query helpers (`getFeaturedProjects()`, `getProjectBySlug(slug)`) as named exports. Keep category labels in a `Record<Category, string>` map — it's the single place to rename them.

### Metadata wired to siteConfig

Use Next.js's `Metadata` type with a `template` for dynamic page titles:

```typescript
export const metadata: Metadata = {
  title: { default: `${siteConfig.name}`, template: `%s | ${siteConfig.name}` },
  openGraph: { url: siteConfig.url, siteName: siteConfig.name, ... },
};
```

Each page then just exports `export const metadata = { title: "Projects" }` and gets `"Projects | Name"` automatically.

### Static generation for content pages

Project detail pages (`/projects/[slug]`) should export `generateStaticParams()` returning `projects.map(p => ({ slug: p.slug }))`. This pre-renders all pages at build time — no server needed for GitHub Pages or Netlify static hosting.

Use the pattern: server component unwraps `await params`, passes `slug` to a `"use client"` component that handles interactivity.

### Header scroll state

Track `isScrolled = window.scrollY > 20` in a `useEffect` with a passive scroll listener and cleanup. Use it to add a background/border to the header after scroll. Also use `usePathname()` to close the mobile menu on route change.

### Accessible filter tabs

Filter buttons on a projects page should use `role="tablist"` / `role="tab"` / `aria-selected`. Drive state with `useMemo` on a typed filter value. Wrap the grid in `AnimatePresence mode="wait"` keyed to the active filter for smooth transitions.

### Modal / lightbox

Keyboard handler: Escape closes, arrows navigate. Always `document.body.style.overflow = "hidden"` when open and restore on cleanup. Use `AnimatePresence` for enter/exit animations; key the inner image to `currentIndex` for per-image transitions.

### cn utility

One-liner class combiner — avoids string concatenation and conditional logic scattered across JSX:

```typescript
export const cn = (...classes: (string | undefined | null | false)[]) =>
  classes.filter(Boolean).join(" ");
```

---

## Bootstrap 3 Specificity Fixes

Bootstrap's `a { color: #337ab7 }` wins over custom CSS unless countered:

```css
.bio-content a, .work-section a { color: var(--accent) !important; }
.work-title a { color: var(--text-primary) !important; }
.work-title a:hover { color: var(--accent) !important; }

/* Prevent <a> wrapper opacity from bleeding onto button children */
.hero-buttons a, .hero-buttons a:hover { color: inherit !important; opacity: 1 !important; }
```

Use `!important` only where Bootstrap specificity genuinely wins — don't scatter it.

---

## GitHub Pages

Always commit a `.nojekyll` file to the repo root. Any `.gemspec` triggers Jekyll processing and breaks the site. For debugging stale deploys: hard refresh → incognito → check Actions tab → wait 10 min for CDN propagation on custom domains.

---

## Key Rules

- **Never touch nav spacing when asked to change content spacing** — identify the exact selector
- **Go bigger on fonts** — especially Bootstrap 3 where the 14px base makes rem sizes deceptively small
- **No `max-width` on content wrappers** — fill the page
- **Name CSS variables by role** (`--accent`, `--text-primary`), not by color name
- **`!important` only for Bootstrap overrides** on link colors
- **Absolute paths** (`/js/`, `/css/`) for all subpages to avoid path resolution bugs
- **Keep `.nojekyll`** in the repo root always
