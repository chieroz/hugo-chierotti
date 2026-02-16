# Hugo Theme Rebuild Design

## Problem

The site uses the hugo-bearblog theme via git submodule. It works but looks barebones — plain Verdana, flat text, `+++` bullet lists, no visual hierarchy. The goal is a professional, clean look while keeping everything that works: Markdown content, Hugo SSG, git-push deploys to Netlify.

## Approach

Replace the Bear Blog theme with a custom theme built on Simple.css — a ~10KB classless CSS library that styles semantic HTML automatically. Dark mode, responsive nav, good typography, all built in.

## Architecture

4 template files + 1 CSS file replace the entire theme:

- `layouts/_default/baseof.html` — HTML skeleton with header/nav/main/footer
- `layouts/_default/single.html` — individual pages and articles
- `layouts/_default/list.html` — section indexes (Progetti, Netiquette)
- `layouts/index.html` — homepage
- `static/css/simple.css` — Simple.css library

## What Gets Removed

- `themes/hugo-bearblog/` git submodule
- `layouts/partials/style.html`
- `layouts/partials/nav.html`
- `layouts/partials/footer.html`
- `layouts/partials/custom_head.html`
- `.gitmodules`

## What Stays Unchanged

- All 40+ Markdown content files
- `netlify.toml`
- `static/static/images/` (favicon, share image)
- Plausible analytics (moves into baseof.html head)
- Git push → Netlify auto-deploy workflow

## Config Changes

- `config.toml`: remove `theme = 'hugo-bearblog'`, update copyright year to 2026

## Page Structure

Every page uses this HTML structure (what Simple.css expects):

```html
<header>
  <h1>chierotti.it</h1>
  <nav>links</nav>
</header>
<main>
  <!-- page content -->
</main>
<footer>
  <p>copyright</p>
</footer>
```

## Result

Same Markdown workflow, same deploy pipeline. Site goes from raw text to clean professional portfolio with proper typography, responsive navigation, automatic dark mode, and good spacing.
