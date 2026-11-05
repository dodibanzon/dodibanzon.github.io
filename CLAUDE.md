# CLAUDE.md

## Repository Overview

**dodibanzon.github.io** is Dodi Banzon's personal developer portfolio site deployed via GitHub Pages. It is a **zero-dependency, single-file static site** — the entire application lives in `index.html`. The design is Apple-inspired: true-black background, frosted-glass nav, gradient accent text, pill buttons, and generous whitespace.

One other page lives alongside it: a private **spending tracker** at `spending/index.html` (served at `/spending/`). It is self-contained the same way and locked with a passcode, and the portfolio nav links to it only in browsers where it has been set up. See [Spending tracker](#spending-tracker).

## File Structure

```
dodibanzon.github.io/
├── index.html           # Entire portfolio (HTML + CSS + JS)
├── spending/
│   └── index.html       # Spending tracker (HTML + CSS + JS), standalone
├── README.md            # Minimal readme
├── WEBFLOW.md           # Site flow: sitemap, navigation map, router/dashboard/form flows (Mermaid)
└── CLAUDE.md            # This file
```

There is no build step, no package manager, and no tooling configuration.

## Architecture

### Single-file SPA

`index.html` is structured in four major sections, in order:

| Section | Description |
|---------|-------------|
| HTML head | Meta tags (including the Content Security Policy and referrer policy), theme-color, favicon (inline SVG, gradient rounded square), description |
| `<style>` | All CSS: design tokens, reset, components, responsive |
| `<body>` | Nav, `#page` mount point, footer, 6 `<template>` elements |
| `<script>` | Client-side router, dashboard renderer, form handler |

### Client-side Routing

Six pages are defined as `<template id="tpl-{page}">` elements and swapped into `<div id="page">` on navigation:

```
#home        → tpl-home
#experience  → tpl-experience
#projects    → tpl-projects
#dashboard   → tpl-dashboard
#about       → tpl-about
#contact     → tpl-contact
```

The router (`navigate()`) performs a 180ms fade-out, clones the template into `#page`, then binds `[data-page]` click handlers inside the new page only (nav/footer links are bound once on init — re-binding them would stack duplicate listeners). History is managed via `history.pushState` and `popstate`.

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
- Responsive breakpoints: `640px` (hamburger nav), `520px` (stacked buttons), `700px` (about layout, bento, dashboard stack)
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
- The Dashboard page uses `.kpi` stat tiles, `.dash-card` panels (same card recipe), a `.tl-*` role timeline, `.sk-*` skill bars, a shared `.dash-tip` tooltip and a `.dash-data` "Data table" disclosure under each chart
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

- **Keep everything in `index.html`** — do not split into separate CSS/JS files unless explicitly asked (the spending tracker is the one other page, and it keeps its own CSS and JS inline too)
- **Use existing CSS variables** for all colors, gradients, and easing
- **Follow BEM-like naming**: `.component-element` pattern
- **Preserve visual effects** (glass nav, gradient text, hero glow, entrance animation, live dot)
- **Maintain the Apple-minimal aesthetic** — restraint is the point: hairline borders, muted grays, color only as accent
- **Keep JS vanilla** — no frameworks, no npm dependencies
- **Add new pages** by adding a `<template id="tpl-{page}">` and registering the page name in the `PAGES` array (a script edit, so update the CSP hash — see [Content Security Policy](#content-security-policy))
- **Keep `WEBFLOW.md` in sync** when you add, remove, or relink a page, or change the router, dashboard, or form flow

### What NOT to do

- Do not introduce npm, bundlers, or build tools unless explicitly requested
- Do not add external CSS frameworks (Tailwind, Bootstrap, etc.)
- Do not change the color scheme without being asked
- Do not add a web font — the system font stack is intentional
- Do not modify the router logic unless fixing a specific bug
- Do not add inline styles — use CSS classes instead (see `.spacer-sm` / `.spacer-lg`)
- Do not add inline event handlers (`onclick="…"`) or `javascript:` links — the CSP blocks them; use `addEventListener`
- Do not load anything from a new origin without adding it to the CSP
- Do not re-introduce heavy decoration (noise overlays, scan lines, borders everywhere) — it breaks the minimal look

### Dashboard

`#dashboard` has no data of its own — `renderDashboard()` reads the `.proj-card`s in `tpl-experience` and `tpl-projects` every time the page mounts, so editing those cards updates it automatically. Keep these card conventions intact:

- `.proj-footer span` on Experience cards is a date range like `Jun 2025 – Present` or `Mar 2025 – May 2025` (drives the timeline and months of experience)
- `.proj-link` text is the short org name (`PIDS ›`)
- Plain and `.cyan` tags are skills (counted in the Toolkit chart); `.green` tags are status (`Current`, `Ongoing` → "In progress") or a result that starts with a number (`−50% response time` → its own key-figure tile)

Chart marks use `--accent` only (single series); every chart has a hover/focus tooltip and a "Data table" twin.

### Contact Form

`handleFormSubmit` POSTs the form as JSON to [Web3Forms](https://web3forms.com) (`https://api.web3forms.com/submit`), which emails the message to `dodibanzon@gmail.com`. The form has no inline `onsubmit`: a `submit` listener on `#page`, bound once on init, calls `handleFormSubmit` whenever the contact page is mounted. The public access key lives in the `WEB3FORMS_KEY` constant next to the handler (safe to commit; changing it is a script edit, so update the CSP hash). While it is still the `YOUR_ACCESS_KEY` placeholder, the form shows a "not set up" message instead of sending. Inputs need `name` attributes (`name`, `email`, `message`), and the hidden `botcheck` checkbox (`.form-hp`) is the honeypot — keep both. Status text goes in `#form-status` (`.ok` / `.err`). The CSP's `connect-src` allows only `https://api.web3forms.com`, so a different endpoint must be added there. Do not implement a real backend without being asked.

### Spending tracker

`spending/index.html` is a personal spending dashboard: monthly spending stacked by category, a category ranking, a weekly summary, week-by-week totals, a transaction list, and a date-range + category filter that scopes all of it. It is a standalone page (not a `<template>` in the portfolio), marked `noindex`. It copies the portfolio's tokens and card/button recipes into its own `<style>`; there are no shared files. Its `.shell` is wider (`1080px`) to give the charts room. The portfolio nav item `#nav-spending` starts `hidden`, and `index.html` reveals it only when this browser's `localStorage` has the tracker's `spending:vault` (or the older `spending:v1`), so visitors never see the link.

**Only the owner can open the data.** GitHub Pages has no logins, so the page and its code are public; the data is what's private. It stays in the browser it was entered in (the page's CSP has no `connect-src`), encrypted:

- `spending:vault` is one AES-GCM ciphertext of `{ mine, currency, rules, txs }`, with a 256-bit key derived from the owner's passcode (PBKDF2-SHA256, 600,000 iterations, random salt, fresh IV on every save). The passcode is never stored, and the key exists only in memory while unlocked. Saves are queued (`save()` → `flush()`), so writes never overlap.
- The page opens on a lock screen (`showLock()`: Private → Create a passcode, Locked, or Protect for older data) and renders nothing until the vault decrypts. Locking reloads the page (`lockNow()`): the Lock button, 15 idle minutes, and a Back/Forward restore all lock it. A forgotten passcode can't be recovered; "Forgot your passcode?" erases this browser's vault. Change passcode re-encrypts with a new salt after checking the current one.
- The first version stored `spending:v1` unencrypted. It is encrypted and deleted when the owner sets a passcode.
- Filter choices in `spending:prefs` aren't sensitive and stay readable.
- Until the first add or import (`mine: false`), it shows a year of seeded sample data from `makeSample()`.

Keep it that way: never store or render expense data before unlock, never write it anywhere but the vault, never commit real spending data to this public repo, and don't add sync, analytics or any network call without being asked.

- **Categories** — `CATS` (Food, Subscriptions, Transport, Entertainment, Travel, Health, Shopping, Bills, Other) is the stacking and legend order. Their colors (`--c-*`, applied through `.cat-{id}` classes that set `--cat`) were validated as a set, in that order, for colorblind separation against the card surfaces. Don't reorder them or add a hue; new kinds of spending fold into Other. Text never takes a category color: a swatch or the mark beside it carries the identity.
- **Auto-categorizing** — `RULES` maps description keywords to a category, first match wins, so specific patterns come before generic ones (GrabFood before Grab, Amazon Prime before Amazon). `CAT_ALIASES` maps an imported file's own category names. When someone changes an expense's category, every expense with the same description moves with it and the choice is remembered in `S.rules` for future imports.
- **Import** — `parseCSV` (comma, semicolon or tab; quoted fields), `guessColumns` (by header name, then by content), `parseDate` (ISO, M/D/Y or D/M/Y detected from the data, month names, spreadsheet serials) and `parseAmount` (₱/commas/parentheses/`DR`·`CR`/decimal commas). With mixed signs, whichever sign most rows have is spending. Rows already present are skipped as duplicates; real repeats in a new file are kept.
- **Charts** — months, weeks and days all go through one `columnChart()`: stacked columns no wider than 24px, a 2px gap between segments, rounded tops, the whole band as the hover target, arrow keys between columns, and a "Data table" twin. One shared `#tip` tooltip. Weeks start on Monday; averages count complete months and weeks only.
- Editing the script changes its hash, so run the check in [Content Security Policy](#content-security-policy) afterwards.

### Content Security Policy

GitHub Pages can't send security headers, so the policy is a `<meta http-equiv="Content-Security-Policy">` tag at the top of `<head>`:

```
default-src 'none'; script-src 'sha256-…'; style-src 'unsafe-inline'; img-src data:;
connect-src https://api.web3forms.com; base-uri 'none'; form-action 'none'
```

**`script-src` pins the inline script by its SHA-256 hash.** Any edit to the script, even a comment or a space, changes the hash, and with a stale hash the browser blocks the whole script, so the site renders blank. Both pages carry their own policy and hash. After editing either script, run this from the repo root and replace the old hash in that page's CSP with the one it prints:

```bash
python3 - <<'EOF'
import base64, hashlib, re
for path in ['index.html', 'spending/index.html']:
    html = open(path, encoding='utf-8').read()
    start = html.rindex('<script>') + len('<script>')
    script = html[start:html.index('</script>', start)]
    want = "'sha256-" + base64.b64encode(hashlib.sha256(script.encode()).digest()).decode() + "'"
    have = re.search(r"script-src ('sha256-[^']*')", html).group(1)
    print(path, want, '(matches the CSP)' if want == have else '(CSP has ' + have + ': update it)')
EOF
```

`spending/index.html` uses the same policy without `connect-src`, so it can't make any network request. Keep it that way.

When the hash is stale, Chrome's DevTools console also prints the one it expects ("…a hash ('sha256-…')…").

- Keep the one `<script>` block: every inline script needs its own hash, and the check above reads the last one. No `eval`, `new Function` or string `setTimeout` either — the CSP blocks them.
- No inline event handlers (`onclick="…"`) or `javascript:` links. Bind with `addEventListener`; for elements inside templates, delegate from `#page` the way the contact form does.
- Anything loaded from another origin needs that origin in the matching directive: a fetch → `connect-src`, an image → `img-src`, a third-party script such as a captcha → `script-src` (often `frame-src` too).
- `style-src 'unsafe-inline'` keeps the `<style>` block editable without a hash. CSS still can't load anything external, because `img-src` and `font-src` stay locked.
- `frame-ancestors` (clickjacking protection) only works as an HTTP header, which GitHub Pages can't set. Browsers ignore it in a meta tag, so don't add it there.

## Key Identifiers

| ID / Class | Purpose |
|---|---|
| `#page` | SPA mount point where template content is rendered |
| `#nav-toggle` | Mobile hamburger button |
| `#nav-links` | Nav link list (toggled `.open` on mobile) |
| `#nav-spending` | Nav item linking to the spending tracker; shown only where the tracker is set up |
| `#year` | Injected with `new Date().getFullYear()` on init |
| `tpl-home/experience/projects/dashboard/about/contact` | Page template elements |
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
