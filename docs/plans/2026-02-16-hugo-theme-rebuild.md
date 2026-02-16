# Hugo Theme Rebuild — Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Replace the hugo-bearblog theme with a custom theme using Simple.css for a clean, professional look.

**Architecture:** 4 Hugo template files + 1 CSS file. Remove the bearblog git submodule and all partials. Content files untouched.

**Tech Stack:** Hugo, Simple.css (classless CSS library), Netlify deploy

---

### Task 1: Download Simple.css

**Files:**
- Create: `static/css/simple.css`

**Step 1: Download Simple.css from CDN**

```bash
mkdir -p static/css
curl -o static/css/simple.css https://cdn.simplecss.org/simple.css
```

**Step 2: Verify the file exists and is reasonable size (~10KB)**

```bash
wc -c static/css/simple.css
```

Expected: around 10000-12000 bytes

**Step 3: Commit**

```bash
git add static/css/simple.css
git commit -m "add Simple.css library"
```

---

### Task 2: Create baseof.html

This is the HTML skeleton every page uses. It replaces the theme's baseof.html and all 4 partials (style.html, nav.html, footer.html, custom_head.html).

**Files:**
- Create: `layouts/_default/baseof.html`

**Step 1: Write baseof.html**

```html
<!DOCTYPE html>
<html lang="{{ with .Site.LanguageCode }}{{ . }}{{ else }}en-US{{ end }}">

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>{{ with .Title }}{{ . }} | {{ end }}{{ .Site.Title }}</title>
  <meta name="description" content="{{ with .Params.description }}{{ . }}{{ else }}{{ .Site.Params.description }}{{ end }}">
  <link rel="icon" type="image/png" href="{{ "static/images/favicon.png" | relURL }}">
  <meta property="og:title" content="{{ .Title }}">
  <meta property="og:description" content="{{ with .Params.description }}{{ . }}{{ else }}{{ .Site.Params.description }}{{ end }}">
  <meta property="og:image" content="{{ "static/images/share.png" | absURL }}">
  <meta property="og:type" content="website">
  <link rel="stylesheet" href="{{ "css/simple.css" | relURL }}">
  <script async defer data-domain="chierotti.it" src="https://plausible.io/js/plausible.js"></script>
</head>

<body>
  <header>
    <h1>{{ .Site.Title }}</h1>
    <nav>
      <a href="{{ "" | relURL }}">Home</a>
      {{ range .Site.Menus.main }}
      <a href="{{ .URL }}">{{ .Name }}</a>
      {{ end }}
      {{ with .Site.GetPage "/netiquette" }}
      <a href="{{ "netiquette" | relURL }}">Netiquette</a>
      {{ end }}
      {{ with .Site.GetPage "/progetti" }}
      <a href="{{ "progetti" | relURL }}">Progetti</a>
      {{ end }}
    </nav>
  </header>

  <main>
    {{ block "main" . }}{{ end }}
  </main>

  <footer>
    <p>&copy; 2003-2026 Carlo Mario Chierotti | P.IVA 01468910052</p>
  </footer>
</body>

</html>
```

**Step 2: Commit**

```bash
git add layouts/_default/baseof.html
git commit -m "add custom baseof.html with Simple.css and nav"
```

---

### Task 3: Create single.html

Replaces the current `layouts/_default/single.html`. Renders individual pages (Bio, Lavoro, Passioni, Triathlon, each project, each netiquette article).

**Files:**
- Modify: `layouts/_default/single.html`

**Step 1: Replace single.html**

```html
{{ define "main" }}
<article>
  {{ if not .Params.menu }}
  <h2>{{ .Title }}</h2>
  {{ end }}
  {{ .Content }}
</article>
{{ end }}
```

Logic: pages in the main menu (Bio, Lavoro, etc.) already have a `# heading` in their Markdown, so we skip the title for those. Section pages (netiquette articles, project pages) get the title rendered as `<h2>`.

**Step 2: Commit**

```bash
git add layouts/_default/single.html
git commit -m "replace single.html with clean article template"
```

---

### Task 4: Create list.html

Replaces the current `layouts/_default/list.html`. Renders section index pages (Progetti, Netiquette).

**Files:**
- Modify: `layouts/_default/list.html`

**Step 1: Replace list.html**

```html
{{ define "main" }}
<article>
  {{ .Content }}
</article>

<ul>
  {{ range .Pages }}
  <li><a href="{{ .Permalink }}">{{ .Title }}</a></li>
  {{ end }}
</ul>
{{ end }}
```

Removes the `+++` prefix and `blog-posts` class. Simple.css styles `<ul>` with proper spacing automatically.

**Step 2: Commit**

```bash
git add layouts/_default/list.html
git commit -m "replace list.html with clean section listing"
```

---

### Task 5: Create index.html

Homepage template. Currently served by the theme's index.html.

**Files:**
- Create: `layouts/index.html`

**Step 1: Write index.html**

```html
{{ define "main" }}
<article>
  {{ .Content }}
</article>
{{ end }}
```

The homepage content is in `content/_index.md` — this just renders it.

**Step 2: Commit**

```bash
git add layouts/index.html
git commit -m "add homepage template"
```

---

### Task 6: Update config.toml

**Files:**
- Modify: `config.toml`

**Step 1: Remove theme line and clean up**

Remove the line `theme = 'hugo-bearblog'` and its comment. Update copyright year to 2026. Remove bearblog-specific comments and params that no longer apply (`hideMadeWithLine`, `dateFormat`).

The result:

```toml
baseURL = "https://chierotti.it"
title = "chierotti.it"
author = "Chieroz"
copyright = "Copyright © 2026, Carlo Mario Chierotti."
languageCode = "it-IT"
enableRobotsTXT = true

disableKinds = ["taxonomy"]
ignoreErrors = ["error-disable-taxonomy"]

[permalinks]
  netiquette = "/:slug/"

[params]
  description = "Sito di Carlo Mario Chierotti, sviluppatore"
  favicon = "images/favicon.png"
  images = ["images/share.png"]
  title = "CMC Website"
```

**Step 2: Commit**

```bash
git add config.toml
git commit -m "remove bearblog theme reference, update copyright year"
```

---

### Task 7: Remove old theme and partials

**Files:**
- Delete: `themes/hugo-bearblog/` (git submodule)
- Delete: `layouts/partials/style.html`
- Delete: `layouts/partials/nav.html`
- Delete: `layouts/partials/footer.html`
- Delete: `layouts/partials/custom_head.html`
- Delete: `.gitmodules`

**Step 1: Remove the git submodule**

```bash
git submodule deinit -f themes/hugo-bearblog
git rm -f themes/hugo-bearblog
rm -rf .git/modules/themes/hugo-bearblog
```

**Step 2: Remove old partials**

```bash
rm layouts/partials/style.html
rm layouts/partials/nav.html
rm layouts/partials/footer.html
rm layouts/partials/custom_head.html
```

**Step 3: Remove .gitmodules if now empty**

```bash
git rm .gitmodules
```

**Step 4: Commit**

```bash
git add -A
git commit -m "remove bearblog theme and old partials"
```

---

### Task 8: Test locally

**Step 1: Run Hugo dev server**

```bash
hugo server -D
```

Expected: site builds without errors and serves on localhost:1313

**Step 2: Verify in browser**

Check each page:
- Homepage: renders intro text with proper headings
- Bio, Lavoro, Passioni, Triathlon: render full content
- Progetti: shows intro + list of project links
- Netiquette: shows intro + list of article links
- Individual project/netiquette pages: render content with title
- Navigation: all links work
- Dark mode: toggle OS preference, verify it flips
- Mobile: resize browser to check responsive nav

**Step 3: Fix any issues found**

**Step 4: Commit any fixes**

---

### Task 9: Update homepage content

**Files:**
- Modify: `content/_index.md`

**Step 1: Update the last line**

The current last line says "Questo sito è fatto con Hugo". Update to remove the Hugo-specific reference or keep it accurate — the site is still made with Hugo, so this line can stay. Only change if the user wants to.

**Step 2: Commit if changed**

---

### Task 10: Final deploy verification

**Step 1: Build the site**

```bash
hugo
```

Expected: site builds to `public/` without errors

**Step 2: Push to remote**

```bash
git push
```

Expected: Netlify picks up the push and deploys automatically

**Step 3: Verify live site at https://chierotti.it**
