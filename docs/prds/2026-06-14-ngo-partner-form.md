# PRD: Dual-Audience Website — Add NGO Partner Form

**Date:** 2026-06-14
**Status:** Updated — requires rebuild for side-by-side dual CTA layout

## Objective

Redesign the YAIL Montevideo website to serve two distinct audiences while
preserving the existing dark-theme aesthetic and single-page layout. Add an
NGO-facing contact pathway: a prominent button linking to an external form
where NGOs can express interest in partnering with YAIL on AI projects.

## Users

| Audience | Who | Need |
|---|---|---|
| Participants | Young people wanting to learn AI, join projects, build community | Current `#unete` section serves them via mailto + external YAIL link |
| NGOs / Institutions | Organizations (NGOs, nonprofits) with tasks that could be enhanced by AI | New pathway: a button/link to a contact form to initiate partnership |

## Current State (baseline)

- Single-page site: hero → about → projects → partners → join
- Hero has two CTAs: "Únete al Hub" (`#unete`) and "Conoce más" (`#sobre`)
- `join.html` (`#unete`) is participant-focused: "¿Te interesa la IA...?"
- `partners.html` (`#socios`) shows a logo grid of partner orgs — no CTA
- All content is Spanish, dark theme, Inter font, gradient accent (blue→purple→pink)
- No form handling exists; only `mailto:` links

## Implemented State

- Single-page site: hero → about → projects → partners → **dual CTA row** → (no separate join section)
- Hero has three CTAs: "Únete al Hub" (`#unete`), "Conoce más" (`#sobre`), **"Para ONGs" (`#asociate`)**
- New **dual CTA section** placed after `#socios`: two cards side-by-side on desktop (stacked on mobile)
  - Left card: "¿Representás a una ONG?" — NGO partnership CTA (links to Google Form + email)
  - Right card: "Únete al Hub" — participant join CTA (mailto + external YAIL link)
- Nav includes "ONGs" link to `#asociate` (left card anchor)
- Form URL stored in `_data/ngo-form.yml` (Google Forms)
- New SCSS partial: `_sass/_dual-cta.scss` for side-by-side layout

## Proposed Changes

### 1. Replace separate `#asociate` and `#unete` sections with a dual CTA row

New include `_includes/dual-cta.html`. Placed **immediately after the partners
section** (`#socios`) on the home page. **Replaces** the separate `partners-cta.html`
and `join.html` includes in `index.html`.

Layout:
- **Desktop (≥768px)**: Two cards side-by-side (50/50), equal height, sharing the
  gradient glow background
- **Mobile (<768px)**: Cards stack vertically, same as current behavior

Left card (`#asociate`):
- Title: "¿Representás a una ONG?"
- Body: Explains that YAIL builds AI solutions for social impact and invites
  NGOs with AI-solvable challenges to get in touch.
- Primary button: "Contactanos" — links to external NGO form (from `_data/ngo-form.yml`)
- Secondary button: "Escribinos por email" — `mailto:` link

Right card (`#unete`):
- Title: "Únete al Hub"
- Body: Participant-focused copy (unchanged from current `join.html`)
- Primary button: "Escribinos" — `mailto:` link
- Secondary button: "Red global YAIL" — external link to YAIL Hub
- Email contact line (unchanged)

Both cards reuse the `.join` visual style (dark background, gradient glow, centered
typography, consistent spacing).

### 2. Update hero.html (minor text / CTA tweak)

Add a third button in `.hero__actions`: "Para ONGs" that links to `#asociate`
(the left card's anchor). The two existing buttons ("Únete al Hub" → `#unete`,
"Conoce más" → `#sobre`) remain unchanged.

`btn--outline` style works for the third button to avoid competing visually.

### 3. Update nav.html

Add "ONGs" nav link pointing to `#asociate`, matching the existing pattern.

### 4. New SCSS partial: `_sass/_dual-cta.scss`

Defines the side-by-side grid layout for the dual CTA row. Added to
`assets/css/main.scss` import list.

### 5. New data file: `_data/ngo-form.yml`

Single key `url:` with the external form link. Keeps the URL centralized and
editable without touching HTML.

## Files Modified / Created

| File | Action | Notes |
|---|---|---|
| `index.html` | Edit | Replace `partners-cta` + `join` includes with single `dual-cta` include |
| `_includes/hero.html` | Edit | Add third CTA button for NGOs |
| `_includes/nav.html` | Edit | Add "ONGs" nav link to `#asociate` |
| `_includes/partners-cta.html` | **Delete** | Replaced by `dual-cta.html` |
| `_includes/join.html` | **Delete** | Replaced by `dual-cta.html` (right card) |
| `_includes/dual-cta.html` | **New** | Dual CTA row with two side-by-side cards |
| `_sass/_dual-cta.scss` | **New** | Grid layout for dual CTA row |
| `assets/css/main.scss` | Edit | Replace `@import "partners-cta"` with `@import "dual-cta"` |
| `_data/ngo-form.yml` | **New** | Stores form URL as structured data |
| `assets/js/main.js` | No change | IntersectionObserver handles both anchors automatically |

## Non-Goals

- No server-side form handling. The form is external (Google Forms, Typeform,
  etc.). We only link to it.
- No redesign of the partners logo grid (`#socios`) — it stays as-is.
- No restructuring of the `#unete` join section — it continues serving
  participants unchanged.
- No i18n changes, no new fonts, no analytics.

## Success Criteria

1. Home page renders a **dual CTA row** after `#socios` containing two cards:
   left card `#asociate` (NGO), right card `#unete` (participants).
2. Desktop (≥768px): cards display side-by-side, equal height, 50/50 width.
3. Mobile (<768px): cards stack vertically, each full-width.
4. Both cards have visible buttons linking to correct URLs (NGO form, mailto, external YAIL).
5. The hero section has a third CTA button ("Para ONGs") linking to `#asociate`.
6. The nav bar includes "ONGs" link pointing to `#asociate`.
7. Visual style matches the existing `.join` section (dark theme, gradient
   glow, consistent typography and spacing).
8. `bundle exec jekyll build` exits cleanly.
9. No regressions: existing sections, project pages, and nav behavior all
   function as before.

## Risks

- **Low risk.** No backend, no data handling, no new dependencies.
- Form URL is external — if it breaks or changes, the `_data/ngo-form.yml`
  file is the single point to update.
- Hero gaining a third button could crowd the CTA area on very narrow
  viewports. The existing `flex-wrap: wrap` on `.hero__actions` handles this
  gracefully, though three stacked buttons on mobile (<360px) may look
  heavy. Acceptable.
- Dual CTA row introduces new CSS Grid layout. Verify equal-height cards
  work correctly with varying content lengths. Use `align-items: stretch`
  and ensure gradient glow covers both cards visually.

## Verification

```bash
# Build the site
bundle exec jekyll build

# Serve and manually inspect:
# - Home page: dual CTA row after #socios with two cards side-by-side on desktop
# - Desktop (≥768px): two equal-height cards, 50/50 width
# - Mobile (<768px): cards stack vertically, full-width
# - Hero: third button "Para ONGs" links to #asociate
# - Nav: "ONGs" link works, scrolls to #asociate (left card)
# - All existing sections intact (#inicio, #sobre, #proyectos, #socios)

# Check no build errors
bundle exec jekyll build --strict_front_matter 2>&1
```

## Open Questions

1. **Form URL**: Where is the NGO contact form hosted? (Google Forms, Typeform,
   custom, etc.) 

2. **Hero button placement**: Third CTA button 

3. **Dual CTA row position**: After partners (`#socios`) — replaces the separate
   `#asociate` + `#unete` sections. Confirm this is the desired final position.

4. **Exact copy**: Need final Spanish text for the NGO card heading and
   body. Proposed draft included in PRD, but needs review by a native speaker
   or the team. Its okay

5. **Card width ratio**: 50/50 split 
