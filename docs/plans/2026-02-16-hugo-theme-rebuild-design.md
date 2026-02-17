# Hugo Theme Rebuild Design

## Problem

The site used the hugo-bearblog theme via git submodule. It worked but looked barebones — plain Verdana, flat text, `+++` bullet lists, no visual hierarchy. The goal was a professional, clean look while keeping everything that works: Markdown content, Hugo SSG, git-push deploys to Netlify.

## Approach

Replaced the Bear Blog theme with a custom theme built on Pico CSS v2 — a classless CSS library with modern typography, responsive nav, and automatic dark mode. Added Inter font, custom accent color, GLightbox for image popups, and a project card grid with thumbnails.

## Architecture

5 template files + section-specific template:

- `layouts/_default/baseof.html` — HTML skeleton with Pico CSS, GLightbox, Plausible analytics, auto-lightbox JS
- `layouts/_default/single.html` — individual pages with back link to parent section
- `layouts/_default/list.html` — default section index (Netiquette)
- `layouts/progetti/list.html` — project grid with thumbnail cards and placeholders
- `layouts/index.html` — homepage

CSS files:
- `static/css/pico.css` — Pico CSS v2 (minified)
- `static/css/glightbox.min.css` — GLightbox styles
- `static/css/custom.css` — Inter font, accent color (#2563eb), header styling, project cards, image thumbnails
- `static/js/glightbox.min.js` — GLightbox library

## What Was Removed

- `themes/hugo-bearblog/` git submodule
- `layouts/partials/style.html`, `nav.html`, `footer.html`, `custom_head.html`
- `.gitmodules`

## What Stayed Unchanged

- `netlify.toml`
- `static/static/images/` (favicon, share image)
- Git push → Netlify auto-deploy workflow

## Config Changes

- `config.toml`: removed `theme = 'hugo-bearblog'`, updated copyright year to 2026, cleaned up comments

## Content Changes

- Projects converted from standalone `.md` files to page bundles (folder with `index.md` + images)
- Placeholder lorem ipsum text replaced with real descriptions for Polomar and Lexicon
- Screenshots added to project pages

## Page Structure

Every page uses this HTML structure (what Pico CSS expects):

```html
<header class="container">
  <h1>chierotti.it</h1>
  <nav>links</nav>
</header>
<main class="container">
  <!-- page content -->
</main>
<footer class="container">
  <p>copyright</p>
</footer>
```

## Key Design Decisions

- **Pico CSS over Simple.css** — Simple.css was tried first but looked dated. Pico has a more modern, refined aesthetic.
- **Font size 110% with !important** — Pico scales font from 100% to 125% across breakpoints. Locking at 110% gives a comfortable reading size on all screens.
- **Inter font** — Clean, modern sans-serif that significantly improves the feel over system defaults.
- **GLightbox** — Auto-wraps article images in lightbox links via JS. No markdown changes needed — just add images to markdown and they become clickable.
- **Page bundles for projects** — Hugo requires page bundles for co-located images. Projects without images get a CSS placeholder showing the first letter of the project title.

## Result

Same Markdown workflow, same deploy pipeline. Site went from raw text dump to a clean professional portfolio with modern typography, responsive navigation, automatic dark mode, image lightbox, and project card grid with thumbnails.
