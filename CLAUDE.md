# CLAUDE.md

## Repository Overview

**dodibanzon.github.io** is Dodi Banzon's personal developer portfolio site deployed via GitHub Pages. It is a **zero-dependency, single-file static site** — the entire application lives in `index.html`. The design is Apple-inspired: true-black background, frosted-glass nav, gradient accent text, pill buttons, and generous whitespace.

## File Structure

```
dodibanzon.github.io/
├── index.html     # Entire application (HTML + CSS + JS)
├── README.md      # Minimal readme
└── CLAUDE.md      # This file
```

There is no build step, no package manager, and no tooling configuration.

## Architecture

### Single-file SPA

`index.html` is structured in four major sections, in order:

| Section | Description |
|---------|-------------|
| HTML head | Meta tags, theme-color, favicon (inline SVG, gradient rounded square), description |
| `<style>` | All CSS: design tokens, reset, components, responsive |
| `<body>` | Nav, `#page` mount point, footer, 4 `<template>` elements |
| `<script>` | Client-side router and form handler |

### Client-side Routing

Four pages are defined as `<template id="tpl-{page}">` elements and swapped into `<div id="page">` on navigation:

```
#home        → tpl-home
#experience  → tpl-experience
#about       → tpl-about
#contact     → tpl-contact
```

The router (`navigate()`) performs a 180ms fade-out, clones the template into `#page`, then re-binds `[data-page]` click handlers. History is managed via `history.pushState` and `popstate`.

## Design System

The aesthetic is **Apple-style minimalism** (think apple.com product pages in dark mode): clean system sans-serif typography, true-black surfaces, hairline borders, large rounded corners, and restrained color used mostly for accents.

### CSS Custom Properties

Defined in `:root`, used throughout — never hardcode colors:

```css
--bg:        #000000                /* Page background (true black) */
--bg2:       #0a0a0c                /* Slightly lifted bg */
--surface:   #1d1d1f                /* Card/panel surfaces (Apple gray) */
--surface2:  #161617                /* Card gradient end */
--border:    rgba(255,255,255,.1)   /* Hairline borders */
--text:      #f5f5f7                /* Primary text (Apple off-white) */
--text2:     #d2d2d7                /* Secondary body text */
--muted:     #86868b                /* Muted/tertiary text (Apple gray) */
--accent:    #2997ff                /* Link blue (Apple dark-mode blue) */
--accent-btn:       #0071e3         /* Primary button blue */
--accent-btn-hover: #0077ed
--purple:    #bf5af2
--cyan:      #64d2ff
--green:     #30d158
--red:       #ff453a
--grad:      linear-gradient(110deg, #2997ff, #bf5af2 55%, #ff375f)  /* Signature gradient */
--ease:      cubic-bezier(.28,.11,.32,1)   /* Apple-style easing, use for all transitions */
```

### Layout

- `.shell` — centered container, `max-width: 980px` (Apple's classic content width), `padding: 0 1.375rem`
- Responsive breakpoints: `520px` (mobile nav/buttons), `700px` (about layout + bento stack)
- Use CSS Grid with `auto-fill` / `auto-fit` + `minmax()` for card grids

### Typography

- Font: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Helvetica Neue', Helvetica, Arial, sans-serif`
- **Never add a web font** — the system font stack is intentional (renders SF Pro on Apple devices, Segoe/Roboto elsewhere)
- Body: 17px (`1.0625rem`), line-height 1.47, antialiased
- Headlines: weight 700, tight letter-spacing (−.02em to −.025em), sentence case with trailing period ("Experience.", "Let's talk.")
- Large intro copy: `1.1875rem`–`1.3125rem` in `--muted`

### Component Conventions

- Section eyebrows use `.section-label` — small semibold text filled with the `--grad` gradient (via `background-clip: text`)
- `.text-grad` — utility for gradient-filled text (used on the hero headline)
- Cards (`.proj-card`, `.about-card`, `.contact-card`, `.bento-tile`, `.form-wrap`) share the recipe: `linear-gradient(180deg, var(--surface), var(--surface2))` background, `1px solid rgba(255,255,255,.08)` hairline, large radius (18–24px), hover = brighter border + slight lift + deep shadow
- Icons (`.proj-icon`, `.contact-icon`) are app-icon-style rounded squares with a faint blue→purple gradient fill and an emoji glyph
- Tags/badges use `.tag` — pill chips (`border-radius: 980px`), `rgba(255,255,255,.07)` fill; `.green` / `.cyan` tinted variants
- Buttons: `.btn` base (pill, `border-radius: 980px`) + `.btn-primary` (solid `--accent-btn` blue, white text) or `.btn-ghost` (translucent white fill)
- Home highlights use the `.bento` grid (`.bento-tile`, `.bento-label`, `.bento-title`, `.bento-text`, `.bento-meta`)
- The Substack callout below the bento uses `.writing` / `.writing-card` (same card recipe, icon + copy + CTA; stacks below 700px)
- Utility spacing: `.pt` / `.pb` (section padding), `.spacer-sm` / `.spacer-lg` (vertical gaps — use instead of inline styles)

### Visual Effects

These are intentional and should be preserved:
- **Frosted-glass nav** — `backdrop-filter: saturate(180%) blur(20px)` over `rgba(22,22,23,.8)` (the apple.com nav recipe)
- **Gradient text** — `--grad` clipped to text on the hero line, section eyebrows, and stat numbers
- **Ambient hero glow** — `.hero-glow`, soft blue/purple radial gradients behind the headline
- **Hero entrance** — staggered `rise` fade-up animation on hero children
- **Live dot** — pulsing green dot (`.live-dot`) on the "Currently" bento tile
- **Reduced motion** — a `prefers-reduced-motion` block disables animation; keep it intact when adding new animation

## Development Workflow

### Running Locally

No build step required. Open `index.html` directly in a browser, or serve with any static server:

```bash
python3 -m http.server 8080
# or
npx serve .
```

### Deployment

Pushing to `main` deploys automatically via GitHub Pages. No CI/CD pipeline exists.

### Branch Strategy

- `main` — production branch, maps directly to the live site
- Feature/fix work should be done on a separate branch and merged via PR

## Editing Guidelines for AI Assistants

### What to do

- **Keep everything in `index.html`** — do not split into separate CSS/JS files unless explicitly asked
- **Use existing CSS variables** for all colors, gradients, and easing
- **Follow BEM-like naming**: `.component-element` pattern
- **Preserve visual effects** (glass nav, gradient text, hero glow, entrance animation, live dot)
- **Maintain the Apple-minimal aesthetic** — restraint is the point: hairline borders, muted grays, color only as accent
- **Keep JS vanilla** — no frameworks, no npm dependencies
- **Add new pages** by adding a `<template id="tpl-{page}">` and registering the page name in the `PAGES` array

### What NOT to do

- Do not introduce npm, bundlers, or build tools unless explicitly requested
- Do not add external CSS frameworks (Tailwind, Bootstrap, etc.)
- Do not change the color scheme without being asked
- Do not add a web font — the system font stack is intentional
- Do not modify the router logic unless fixing a specific bug
- Do not add inline styles — use CSS classes instead (see `.spacer-sm` / `.spacer-lg`)
- Do not re-introduce heavy decoration (noise overlays, scan lines, borders everywhere) — it breaks the minimal look

### Contact Form

The form submission handler (`handleFormSubmit`) is a **stub** — it only updates the button state visually. To wire it up, replace the stub with a fetch call to a backend service (Formspree, Web3Forms, etc.). Do not implement a real backend without being asked.

## Key Identifiers

| ID / Class | Purpose |
|---|---|
| `#page` | SPA mount point where template content is rendered |
| `#nav-toggle` | Mobile hamburger button |
| `#nav-links` | Nav link list (toggled `.open` on mobile) |
| `#year` | Injected with `new Date().getFullYear()` on init |
| `tpl-home/experience/about/contact` | Page template elements |
| `.shell` | Max-width centered container (980px) |
| `.nav`, `.nav-inner` | Sticky frosted-glass top navigation |
| `.brand` | Logo/name in nav (`.brand-dim` mutes the surname) |
| `.section-label` | Gradient eyebrow above section headings |
| `.text-grad` | Gradient-filled text utility |
| `.hero`, `.hero-glow`, `.hero-eyebrow`, `.hero-desc` | Home hero |
| `.bento`, `.bento-tile` | Home highlights grid |
| `.writing`, `.writing-card` | Substack callout on the home page |
| `.live-dot` | Pulsing green status dot |
| `.proj-card` | Experience card component |
| `.tag` | Skill/tech badge (pill chip) |
| `.btn`, `.btn-primary`, `.btn-ghost` | Pill button variants |
| `.leaving` | Class added to `#page` during page exit animation |
