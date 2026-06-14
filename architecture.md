# Architecture: YAIL Montevideo Website

## Summary

Static website for the **Young AI Leaders (YAIL)** hub in Montevideo, Uruguay.
Built with **Jekyll** and hosted on **GitHub Pages**. v1.0.0 released 11/06/2026.

Single-page scrolling home (index) with five sections, plus auto-generated
detail pages for each project in the `_projects/` collection.

## High-Level Data Flow

```
_partners.yml ───┐
                 ├──► _includes/*.html ──► _layouts/default.html ──► index.html ──► Jekyll build ──► _site/
_projects/*.md ──┘                                      │
                                                         │ (project pages)
                                                         └──► _layouts/project.html ──► /proyectos/:name/
```

All pages inherit from `_layouts/default.html` which provides the HTML shell
(`<head>`, nav, footer). Project markdown files use `_layouts/project.html`
which in turn inherits `default`.

## Static Site Graph

```
index.html
│
├── include hero.html           ← section#inicio
├── include about.html          ← section#sobre       (hardcoded text)
├── include projects.html       ← section#proyectos   (iterates site.projects)
├── include partners.html       ← section#socios      (iterates site.data.partners)
└── include join.html           ← section#unete       (mailto + external link)

Layout chain:
  _layouts/default.html          ← HTML shell (head, nav, main, footer, main.js)
    └── _layouts/project.html    ← project detail page wrapper
```

## Project Map

| Area | Path(s) | Purpose |
|---|---|---|
| Configuration | `_config.yml` | Jekyll config: title, locale, collections, plugins, SASS, exclusions |
| Dependencies | `Gemfile` | Single gem: `github-pages` (pulls Jekyll + plugins via Bundler) |
| Home page | `index.html` | SPA-style landing; includes all five sections in order |
| Layouts | `_layouts/default.html`, `_layouts/project.html` | HTML shell and project detail template |
| Includes | `_includes/head.html`, `nav.html`, `hero.html`, `about.html`, `projects.html`, `partners.html`, `join.html`, `footer.html` | Reusable HTML components; one per page section |
| Collection (content) | `_projects/*.md` | 3 project entries with YAML front matter + Markdown body |
| Structured data | `_data/partners.yml` | Partner list (name, logo, url) |
| Styles | `assets/css/main.scss`, `_sass/_*.scss` | Single entry point importing 8 partials |
| JavaScript | `assets/js/main.js` | Nav: sticky shadow on scroll, hamburger toggle (mobile), active-section highlighting via IntersectionObserver |
| Static assets | `assets/images/` | Logo (PNG), favicon (PNG), partner logos (SVG/PNG) |
| CI/CD | `.github/workflows/jekyll.yml` | GitHub Actions: build + deploy to Pages on push to `main` |
| Docs | `README.md`, `CONTRIBUTING.md`, `CHANGELOG.md` | Onboarding, content-editing guide, release history |

## URL Routing

| URL | Source | Layout |
|---|---|---|
| `/` | `index.html` | `default` (via front matter) |
| `/proyectos/fauna-local/` | `_projects/fauna-local.md` | `project` |
| `/proyectos/buen-provecho-chatbot/` | `_projects/buen-provecho-chatbot.md` | `project` |
| `/proyectos/ia-para-el-bien/` | `_projects/ia-para-el-bien.md` | `project` |

Permalink pattern for projects: `/proyectos/:name/` (configured in `_config.yml`).
No other routes exist.

## CSS Architecture (SCSS)

```
assets/css/main.scss           ← entry point (front-matter dashes required for Jekyll SASS)
  _sass/_variables.scss        ← design tokens: colors, spacing, typography, mixins
  _sass/_base.scss             ← reset, body, .container, .section, .btn, .tag utilities
  _sass/_nav.scss              ← sticky nav, logo, links, hamburger menu (mobile breakpoint 768px)
  _sass/_hero.scss             ← full-height hero with radial glow pseudo-element
  _sass/_cards.scss            ← project cards grid, partner logo cards, hover effects
  _sass/_sections.scss         ← about layout (2-col grid) and value cards
  _sass/_join.scss             ← CTA section with gradient glow background
  _sass/_footer.scss           ← footer bar with flex layout
  _sass/_project-page.scss     ← project detail: hero, prose content, back-link, .card--link hover
```

**Design system**: dark theme (`#09090B` base), gradient accent (blue → purple → pink), Inter font, BEM-like class naming (`block__element--modifier`).

## JavaScript (Vanilla, no framework)

Single file `assets/js/main.js` (~50 lines), wrapped in IIFE. Three concerns:
1. **Sticky nav shadow** — adds `.scrolled` class when `scrollY > 50`
2. **Hamburger menu** — toggles `.open` on nav links + toggle button, closes on link click
3. **Active section** — IntersectionObserver with `rootMargin: -40% 0px -55% 0px` highlights the nav link matching the visible section

## Jekyll Configuration Detail

- **Locale**: `es` (Spanish)
- **Timezone**: `America/Montevideo`
- **Collections**: `projects` → output=true, permalink `/proyectos/:name/`
- **Plugins**: `jekyll-seo-tag`, `jekyll-sitemap` (via `github-pages` gem)
- **SASS**: compressed output
- **Excludes**: Gemfile, Gemfile.lock, README.md, CONTRIBUTING.md, vendor/

## Content Management

- **Projects**: create/edit `.md` files in `_projects/`. Front matter fields:
  `layout`, `title`, `description`, `status` (En curso / Próximamente / En desarrollo / Finalizado), `tags[]`. Filename determines URL slug.
- **Partners**: edit `_data/partners.yml`. Each entry: `name`, `logo` (path to `assets/images/partners/`), `url`. Placeholder logos are grayscale by default, color on hover.

## Build & Deploy

### Local development
```bash
bundle install
bundle exec jekyll serve            # → http://localhost:4000
```

### Production deployment
Push to `main` → GitHub Actions workflow `jekyll.yml` triggers:
1. Checkout + setup Ruby 3.1 with Bundler cache
2. `bundle exec jekyll build --baseurl <pages_base_path>` (JEKYLL_ENV=production)
3. Upload artifact (`_site/`)
4. Deploy to GitHub Pages environment

## Plugin Dependencies

| Plugin | Purpose | Source |
|---|---|---|
| `jekyll-seo-tag` | Auto-generates `<meta>`, JSON-LD, Open Graph tags via `{% seo %}` | Bundled in `github-pages` gem |
| `jekyll-sitemap` | Auto-generates `/sitemap.xml` | Bundled in `github-pages` gem |

## External Dependencies (Runtime)

| Dependency | Purpose | Location |
|---|---|---|
| Google Fonts (Inter) | Typography, weights 400–800 | `_includes/head.html` (3 `<link>` tags) |
| `ruby/setup-ruby` | CI Ruby setup | `.github/workflows/jekyll.yml` (pinned SHA) |
| `actions/configure-pages@v5` | CI Pages config | `.github/workflows/jekyll.yml` |
| `actions/upload-pages-artifact@v3` | CI artifact upload | `.github/workflows/jekyll.yml` |
| `actions/deploy-pages@v5` | CI deployment | `.github/workflows/jekyll.yml` |

No other external API calls, CDN scripts, or tracking.

## Risks / Unknowns

- **Partner links open in new tab** without `noopener` on one card (partners.yml has `#` URLs for placeholders A/B; real one uses `buenprovechoapp.com`). Minor.
- **Nav `Inicio` link always active** — the IntersectionObserver rootMargin means `#inicio` nav link is always lit when page loads. Cosmetic.
- **No form handling** — the "Únete" CTA only uses `mailto:` links. No server-side contact form, no email obfuscation.
- **No analytics or cookie notice** — no GA, Plausible, etc. No GDPR banner.
- **No 404 page** — missing custom 404.html.

## Version

**1.0.0** (11/06/2026) — initial release. 3 projects, 3 partners (2 placeholder), single-page + project details, dark theme, responsive nav.
