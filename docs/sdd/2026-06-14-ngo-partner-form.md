c# SDD: Dual CTA Row — NGO Partner Form + Join Hub

**PRD:** [2026-06-14-ngo-partner-form.md](../prds/2026-06-14-ngo-partner-form.md)
**Date:** 2026-06-14
**Status:** Ready for build (updated for side-by-side layout)

## Context

Single-page Jekyll site with five sections (hero → about → projects → partners → join). The PRD replaces the separate `#asociate` and `#unete` sections with a **dual CTA row** placed after `#socios`: two cards side-by-side on desktop (stacked on mobile). Left card = NGO partnership CTA (`#asociate`), right card = participant join CTA (`#unete`). Also adds a third hero button ("Para ONGs") and a nav link ("ONGs"). All content changes are static HTML/CSS — no backend, no new dependencies.

## Design

### Architecture (existing + new)

```text
                    index.html (include order)
                        |
           hero  about  projects  partners  [NEW: dual-cta]
             |                       |
        hero.html              partners.html
        (edit +btn)              (no change)
             |
        nav.html (edit +link)
             |
        _data/ngo-form.yml (NEW — single url: key)
             |
        _includes/dual-cta.html (NEW — replaces partners-cta.html + join.html)
             |
        _sass/_dual-cta.scss (NEW — grid layout for side-by-side cards)
             |
        assets/css/main.scss (edit: replace partners-cta import with dual-cta)
```

### New Data File: `_data/ngo-form.yml`

Single key/value. Template accesses via `site.data.ngo-form.url`. Placeholder URL until the real form URL is known.

```yaml
url: "https://example.com/ngo-partner-form"  # TODO: replace with real form URL
```

### New Include: `_includes/partners-cta.html`

Matches `.join` visual structure:

```html
<section id="asociate" class="join">    <!-- reuse .join class for identical styling -->
  <div class="container">
    <div class="join__inner">
      <h2 class="join__title">¿Representás a una ONG?</h2>
      <p class="join__subtitle">...</p>
      <div class="join__actions">
        <a href="{{ site.data.ngo-form.url }}" class="btn btn--primary" target="_blank" rel="noopener">Contactanos</a>
        <a href="mailto:mvdhub.youngaileaders@gmail.com" class="btn btn--outline">Escribinos por email</a>
      </div>
    </div>
  </div>
</section>
```

**Decision:** Reuse `.join` class directly instead of creating a new SCSS partial. The PRD says "Uses the same `.join` visual style … to stay consistent." Same DOM structure, same class names, same styles. The `_data/ngo-form.yml` is still created to centralize the URL. The `_sass/_partners-cta.scss` file from the PRD is **not needed** — it would be a copy-paste of `_join.scss`. The `assets/css/main.scss` import for it is also not needed.

**Rationale:** Simpler. Fewer files. Zero risk of visual drift. The IntersectionObserver already handles any `section[id]` — `#asociate` is picked up automatically. The `.join` CSS class already handles: dark background, gradient glow, centered layout, responsive flex-wrap on buttons, typography, spacing — everything the PRD asks for.

### Hero Edit: Third Button

Add one line to `.hero__actions` in `_includes/hero.html`:

```html
<a href="#asociate" class="btn btn--outline">Para ONGs</a>
```

Placed after the two existing buttons. Uses `btn--outline` (transparent + border) to avoid competing with the primary "Únete al Hub" button. Three buttons wrap gracefully on narrow viewports via existing `flex-wrap: wrap`.

### Nav Edit: "ONGs" Link

Add one line in `_includes/nav.html`, before the `nav__cta` item:

```html
<li><a href="#asociate">ONGs</a></li>
```

The existing IntersectionObserver (line 35: `querySelectorAll('section[id]')`) automatically highlights this link when `#asociate` is in view. The existing hamburger close handler (line 25: `querySelectorAll('a[href^="#"]')`) automatically closes the mobile menu on click.

### index.html Edit: Include Order

Add `{% include partners-cta.html %}` between `{% include partners.html %}` and `{% include join.html %}`. Final order:

```
hero → about → projects → partners → partners-cta → join
```

### Files Summary

| File | Action | Lines changed |
|---|---|---|
| `_data/ngo-form.yml` | **Create** | 1 line |
| `_includes/partners-cta.html` | **Create** | ~12 lines |
| `_includes/hero.html` | Edit | +1 line |
| `_includes/nav.html` | Edit | +1 line |
| `index.html` | Edit | +1 line |
| `_sass/_partners-cta.scss` | **Skip** | Reuse `.join` class instead |
| `assets/css/main.scss` | **No change** | No new import needed |
| `assets/js/main.js` | **No change** | IntersectionObserver handles automatically |

## Interfaces

None changed. All sections remain `section[id]`. Nav, hero, and scroll behavior unchanged.

## Tasks

### Task 1: Create `_data/ngo-form.yml`

**Acceptance:** File exists with a `url:` key pointing to a placeholder URL.
**Verification:** `cat _data/ngo-form.yml` shows `url: "https://..."`.

### Task 2: Create `_includes/partners-cta.html`

**Acceptance:** Section renders with `id="asociate"`, `class="join"`, title, body text, two buttons (one links to `site.data.ngo-form.url`, one `mailto:`).
**Verification:** Inspect generated HTML — `#asociate` exists, button `href` matches data URL.

### Task 3: Add NGO button to `_includes/hero.html`

**Acceptance:** Third button `<a href="#asociate" class="btn btn--outline">Para ONGs</a>` in `.hero__actions`.
**Verification:** Inspect hero section in browser — three buttons visible, third links to `#asociate`.

### Task 4: Add "ONGs" nav link to `_includes/nav.html`

**Acceptance:** `<li><a href="#asociate">ONGs</a></li>` added before the `nav__cta` item.
**Verification:** Nav bar shows "ONGs" link. Click scrolls to `#asociate`. Active highlighting works.

### Task 5: Add partners-cta include to `index.html`

**Acceptance:** `{% include partners-cta.html %}` placed between `{% include partners.html %}` and `{% include join.html %}`.
**Verification:** Home page section order: hero → about → projects → partners → NGO-CTA → join.

### Task 6: Build verification

**Acceptance:** `bundle exec jekyll build` exits 0. No warnings.
**Verification:** Run `bundle exec jekyll build --strict_front_matter 2>&1` — clean exit.

### Task 7: Manual visual verification

**Acceptance:** Dark theme, gradient glow, typography, spacing match `.join` section. Mobile layout responsive — no overflow, buttons stack on narrow viewports. Existing sections unchanged.
**Verification:** `bundle exec jekyll serve` → browser inspection at desktop and mobile viewports.

## Risks

- **Form URL missing:** The PRD itself flags this as blocking. The `_data/ngo-form.yml` uses a placeholder URL `https://example.com/ngo-partner-form`. The real URL must be substituted before deployment. This is a one-line change — no code risk.
- **Hero button crowding on mobile:** Three stacked buttons at <480px — the PRD acknowledges this is acceptable.
- **No regressions expected:** No existing files are modified beyond adding single lines. No CSS changes. No JS changes. Build is additive only.
