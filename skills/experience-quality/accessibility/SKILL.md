---
name: accessibility
version: 1.0.0
description: |
  Make an image-led portfolio usable by keyboard and screen reader and compliant
  with WCAG 2.2 AA — alt text for photographs, focus management, colour, motion,
  and forms. Use when building any component, before any launch, or when an
  accessibility issue is reported.
allowed-tools:
  - Read
  - Write
  - Edit
  - Grep
  - Glob
  - Bash
---

## Accessibility

A portfolio has a specific accessibility profile: **very little text, enormous
amounts of imagery, custom galleries, and heavy motion.** That combination fails
in predictable ways — undescribed photographs, lightboxes that trap or lose
focus, animations that cannot be turned off, and low-contrast "elegant" text.

**Target: WCAG 2.2 Level AA.** Treat it as the floor, not the goal.

> **Before running anything:** this skill is a lane, not a phase. Retrofitting
> accessibility after a design is signed off costs several times more than
> building it in, and some decisions (colour, motion, gallery pattern) cannot be
> reversed cheaply.

### Method

1. **Automate what can be automated** — catches roughly a third of issues.
2. **Test with the keyboard only.** Unplug the mouse.
3. **Test with a screen reader** on the real flows.
4. **Check the things tools cannot see** — alt text quality, focus order, motion.
5. **Record what was tested and what was not.**

### Alt text for event photography

The hardest and most-skipped part. Automated tools verify an `alt` attribute
exists; they cannot tell you it is useless.

| Situation | Alt text |
|---|---|
| Photograph carrying content | Describe **what is happening and what matters** — "The bride's mother adjusting her veil moments before the ceremony" |
| Purely decorative | `alt=""` — empty, not missing. The image is then skipped |
| Image inside a link | Describe the **destination**, not the picture — "The Sharma wedding, February 2026" |
| Caption already describes it | `alt=""` and let the caption do the work; do not duplicate |
| Logo | The organisation name |

**Rules:**

- **Under 125 characters.** Longer belongs in a caption.
- **Never start with "Image of" or "Photo of".** Screen readers already announce
  the role.
- **Never use the filename.** `alt="DSC_4471.jpg"` is worse than nothing.
- **Describe what matters *here*.** The same photograph in a gallery and in a
  case study about a venue change may warrant different alt text.
- **Do not describe people's appearance** beyond what is relevant. Describe the
  moment.

The content schema requires `alt` with a real minimum length precisely because
this gets skipped — see `content-collections`.

### Keyboard

Every interactive element must be reachable and operable with `Tab`, `Enter`,
`Space`, arrows, and `Esc`.

- **Visible focus indicator on everything**, meeting 3:1 against its background.
  Never `outline: none` without a replacement. See `color-mood`.
- **Focus order follows visual order.** CSS `order`, `grid-auto-flow`, and
  absolute positioning all break this. WCAG SC 2.4.3.
- **A skip link** to `#main`, visible on focus.
- **No keyboard traps** — SC 2.1.2. Lightboxes are the usual offender; see
  `gallery-patterns`.
- **Focus must not be obscured** by a sticky header — WCAG 2.2 SC 2.4.11. Use
  `scroll-margin-top` on focusable targets.

```css
:focus-visible {
  outline: 2px solid var(--n-0);
  box-shadow: 0 0 0 4px var(--n-900);
  outline-offset: 2px;
}
:target, a:focus-visible { scroll-margin-top: 6rem; }  /* clear the sticky header */

.skip-link { position: absolute; left: -9999px; }
.skip-link:focus { left: var(--space-s); top: var(--space-s); z-index: 100; }
```

### Structure and semantics

- **One `<h1>` per page**; heading levels never skip.
- **Landmarks**: `<header>`, `<nav>`, `<main>`, `<footer>`. One `<main>`.
- **Real elements.** `<button>` for actions, `<a>` for navigation. A `<div>` with
  a click handler is not keyboard-operable and not announced.
- **Lists for lists** — a gallery is a `<ul>`.
- **`lang`** on `<html>` and on every foreign-language run; see
  `multilingual-content`.

### Forms

Highest-stakes flow on the site — a failed enquiry form is a lost customer.

- **Visible `<label>` for every field**, associated by `for`/`id`. Never
  placeholder-only — SC 3.3.2. See `web-copywriting`.
- **Errors identified in text**, adjacent to the field, and associated with
  `aria-describedby` — SC 3.3.1, 3.3.3.
- **Do not signal errors by colour alone** — SC 1.4.1.
- **`autocomplete` attributes** on name, email, tel — SC 1.3.5.
- **Announce submission result** in an `aria-live` region — SC 4.1.3.
- **WCAG 2.2 SC 3.3.7 Redundant Entry**: do not make someone re-enter information
  they already gave.

### Touch targets

WCAG 2.2 SC 2.5.8: minimum **24×24 CSS px**; **44×44 is the practical target**
for a site used one-handed on a phone. Gallery close buttons and nav toggles are
the usual failures.

### Motion and media

Covered fully in `motion-design` and `video-on-web`. The floor:

- `prefers-reduced-motion` honoured, and **no content left hidden** when
  animation is disabled
- Nothing auto-plays for more than 5 seconds without a pause control — SC 2.2.2
- No audio autoplay — SC 1.4.2
- Captions on all speech — SC 1.2.2

### Testing

```bash
# Automated — catches roughly a third of issues
npm i -D @axe-core/playwright playwright
npx playwright test a11y.spec.js

# Whole-site crawl
npx pa11y-ci --sitemap https://studio.example/sitemap-index.xml

# Static checks
grep -rn '<img' src/ --include=*.astro | grep -v 'alt='          # missing alt
grep -rniE 'alt="(image|photo|picture|DSC|IMG)' src/             # useless alt
grep -rn 'outline:\s*none' src/ --include=*.css                   # removed focus
grep -rn 'onclick' src/ --include=*.astro | grep '<div\|<span'    # fake buttons
grep -rn '<h[1-6]' src/ --include=*.astro | head -40              # heading order
grep -rn 'tabindex="[1-9]' src/                                    # positive tabindex
```

```js
// a11y.spec.js
import { test, expect } from '@playwright/test';
import AxeBuilder from '@axe-core/playwright';

for (const path of ['/', '/work/', '/work/sharma-wedding-2026/', '/contact/']) {
  test(`a11y ${path}`, async ({ page }) => {
    await page.goto(path);
    const results = await new AxeBuilder({ page })
      .withTags(['wcag2a', 'wcag2aa', 'wcag21aa', 'wcag22aa'])
      .analyze();
    expect(results.violations).toEqual([]);
  });
}
```

**Automation is necessary and not sufficient.** It cannot judge alt-text quality,
focus order sense, or whether a transition is disorienting.

### Manual testing

1. **Keyboard only** — every page, every interaction, including the lightbox and
   the form. Can you always see where you are?
2. **Screen reader** — VoiceOver (macOS/iOS), NVDA (Windows, free), or TalkBack
   (Android). Navigate by heading, then by landmark. Does the gallery make sense?
3. **400% zoom** — SC 1.4.4 and 1.4.10. No horizontal scrolling, nothing clipped.
4. **Reduced motion enabled** — is all the content still there?
5. **Windows High Contrast / forced colours** — do custom-styled elements survive?

### Caveats

- **AA is a floor.** Passing it does not mean the site is pleasant to use with
  assistive technology.
- **Automated tools disagree with each other**, and a clean axe run means little
  on a site whose content is photographs.
- **A screen reader in the hands of a sighted developer is a smoke test**, not a
  usability test. Budget for a real user if the stakes justify it.
- **Alt text is content work, not engineering work.** It belongs to whoever
  writes the case study, enforced by the schema.

### Checklist

- [ ] Every image has appropriate alt text; decorative images `alt=""`
- [ ] No filename or "image of" alt text
- [ ] Focus visible everywhere at ≥3:1; never removed
- [ ] Focus order matches visual order; nothing obscured by sticky headers
- [ ] Skip link present and visible on focus
- [ ] No keyboard traps; lightbox returns focus to its opener
- [ ] One `<h1>`; heading levels unskipped; landmarks present
- [ ] Real `<button>`/`<a>` elements throughout
- [ ] Every form field labelled; errors in text and associated
- [ ] `autocomplete` on personal-data fields
- [ ] Submission result announced via `aria-live`
- [ ] Touch targets ≥24 px, ideally ≥44 px
- [ ] `prefers-reduced-motion` honoured with no hidden content
- [ ] Captions on all speech; no audio autoplay
- [ ] Contrast 4.5:1 body, 3:1 large and UI
- [ ] axe clean on every template
- [ ] Manual keyboard, screen-reader, 400%-zoom, and forced-colours passes done
- [ ] What was and was not tested is recorded

## References

- **WCAG 2.2** — the full specification; criteria cited inline above
  <https://www.w3.org/TR/WCAG22/>
- **WCAG 2.2 — new criteria**: SC 2.4.11 Focus Not Obscured, SC 2.5.8 Target Size
  (Minimum), SC 3.3.7 Redundant Entry
  <https://www.w3.org/WAI/standards-guidelines/wcag/new-in-22/>
- **WAI-ARIA Authoring Practices Guide** — keyboard patterns for dialogs,
  carousels, disclosure <https://www.w3.org/WAI/ARIA/apg/>
- **W3C WAI — "An alt Decision Tree"**, the source of the alt-text situation table
  <https://www.w3.org/WAI/tutorials/images/decision-tree/>
- **The A11Y Project — checklist** <https://www.a11yproject.com/checklist/>
- **axe-core** (MPL-2.0) and **@axe-core/playwright**
  <https://github.com/dequelabs/axe-core>
- **pa11y-ci** — sitemap-driven crawling <https://github.com/pa11y/pa11y-ci>
- **Inclusive Components — Heydon Pickering**, on focus management in galleries
  and modals <https://inclusive-components.design/>

**Not sourced — written for this framework:** the event-photography alt-text
guidance, the "same photo may need different alt in different contexts" rule, the
manual testing sequence, and the detection commands. The alt decision table is
adapted from the W3C WAI decision tree cited above.
