# PRD: Dual-Audience Website — Add NGO Partner Form

**Date:** 2026-06-14
**Status:** Draft — pending approval and form URL

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

## Proposed Changes

### 1. Add a new section: `#asociate` (NGO partner CTA)

New include `_includes/partners-cta.html`. Placed **immediately after the partners
section** (`#socios`) on the home page. Uses the same `.join` visual style
(gradient glow background, centered layout) to stay consistent.

Contains:
- Title: "¿Representás a una ONG?" (or similar)
- Body: Explains that YAIL builds AI solutions for social impact and invites
  NGOs with AI-solvable challenges to get in touch.
- Single primary button: "Contactanos" / "Asociate con YAIL" — links to the
  external NGO form (URL TBD).
- Optionally a secondary `mailto:` link for direct email.

### 2. Update hero.html (minor text / CTA tweak)

Add a third button in `.hero__actions`: "Para ONGs" that links to `#asociate`.
The two existing buttons ("Únete al Hub" → `#unete`, "Conoce más" → `#sobre`)
remain unchanged.

`btn--outline` style works for the third button to avoid competing visually.

### 3. Update nav.html (optional)

Add "ONGs" nav link pointing to `#asociate`, or keep nav as-is and rely on
the hero button. Decision: add a nav link for discoverability, matching the
existing pattern.

### 4. New SCSS partial: `_sass/_partners-cta.scss`

Extends `.join`-like styles. Added to `assets/css/main.scss` import list.

### 5. New data file: `_data/ngo-form.yml` (optional)

Single key `url:` with the external form link. Keeps the URL centralized and
editable without touching HTML.

## Files Modified / Created

| File | Action | Notes |
|---|---|---|
| `index.html` | Edit | Add `{% include partners-cta.html %}` after partners include |
| `_includes/hero.html` | Edit | Add third CTA button for NGOs |
| `_includes/nav.html` | Edit | Add "ONGs" nav link to `#asociate` |
| `_includes/partners-cta.html` | **New** | NGO partnership CTA section |
| `_sass/_partners-cta.scss` | **New** | Styles for the new section |
| `assets/css/main.scss` | Edit | Add `@import "partners-cta";` |
| `_data/ngo-form.yml` | **New** | Stores form URL as structured data |
| `assets/js/main.js` | No change | Existing IntersectionObserver handles any new `section[id]` automatically |

## Non-Goals

- No server-side form handling. The form is external (Google Forms, Typeform,
  etc.). We only link to it.
- No redesign of the partners logo grid (`#socios`) — it stays as-is.
- No restructuring of the `#unete` join section — it continues serving
  participants unchanged.
- No i18n changes, no new fonts, no analytics.

## Success Criteria

1. Home page renders a new section `#asociate` between `#socios` and `#unete`
   (or at another approved position).
2. The section contains a visible button linking to the NGO form URL.
3. The hero section has a third CTA button ("ONGs" / "Para ONGs") linking to
   `#asociate`.
4. The nav bar includes a link to `#asociate` (or `#socios` if the CTA lives there).
5. Visual style matches the existing `.join` section (dark theme, gradient
   glow, consistent typography and spacing).
6. Mobile layout is usable (responsive flex/wrap, no overflow).
7. `bundle exec jekyll build` exits cleanly.
8. No regressions: existing sections, project pages, and nav behavior all
   function as before.

## Risks

- **Low risk.** No backend, no data handling, no new dependencies.
- Form URL is external — if it breaks or changes, the `_data/ngo-form.yml`
  file is the single point to update.
- Hero gaining a third button could crowd the CTA area on very narrow
  viewports. The existing `flex-wrap: wrap` on `.hero__actions` handles this
  gracefully, though three stacked buttons on mobile (<360px) may look
  heavy. Acceptable.

## Verification

```bash
# Build the site
bundle exec jekyll build

# Serve and manually inspect:
# - Home page: new #asociate section present, hero has third button
# - Nav: "ONGs" link works, scrolls to #asociate
# - Mobile viewport: layout works, no overflow
# - All existing sections intact (#inicio, #sobre, #proyectos, #socios, #unete)

# Check no build errors
bundle exec jekyll build --strict_front_matter 2>&1
```

## Open Questions

1. **Form URL**: Where is the NGO contact form hosted? (Google Forms, Typeform,
   custom, etc.) **Blocking** — cannot complete without this.

2. **Hero button placement**: Third CTA button or replace "Conoce más" with
   "Para ONGs"? Proposed: add third button, keep both existing. Confirm.

3. **NGO section position**: After partners (`#socios`) or after join (`#unete`)?
   Proposed: after partners, before join, so the flow is:
   hero → about → projects → partners → NGO-CTA → join. Confirm.

4. **Exact copy**: Need final Spanish text for the NGO section heading and
   body. Proposed draft included in PRD, but needs review by a native speaker
   or the team.
