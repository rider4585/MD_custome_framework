---
name: accessibility-media
version: 1.1.0
description: |
  The accessibility problems that are specific to image- and video-led pages —
  alt text for photographs, motion and autoplay, touch targets, and the manual
  testing pass automated tools cannot do. Use alongside `accessibility` when a
  page is mostly imagery, when adding a gallery, lightbox, or video, or before
  any launch of a media-heavy site.
allowed-tools:
  - Read
  - Write
  - Edit
  - Grep
  - Glob
  - Bash
---

## Accessibility for Media-Led Pages

**This skill assumes `accessibility` and does not repeat it.** Load that one
first for semantics, keyboard operation, focus, contrast, forms, and modals —
the general rules that apply to every interface. This skill covers only what is
different when the page is mostly pictures and video.

A media-led page has a specific accessibility profile: **very little text,
enormous amounts of imagery, custom galleries, and heavy motion.** That
combination fails in predictable ways — undescribed photographs, lightboxes that
trap or lose focus, animations that cannot be turned off, and low-contrast
"elegant" text over a photograph.

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

### Alt text for photography

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

### Covered by `accessibility`, not repeated here

Keyboard operation and focus, structure and landmarks, form labelling and error
association, contrast ratios, and modal/dialog behaviour are all in the
`accessibility` skill. Two of them have a media-specific twist worth naming:

- **Lightboxes are the usual keyboard trap.** A gallery overlay is a modal: it
  needs focus capture on open, `Esc` to close, focus returned to the thumbnail
  that opened it, and the background made inert → `gallery-patterns`.
- **Never put text directly on an unmodified photograph.** Contrast against an
  image is not a fixed ratio. Use a scrim and verify against the darkest value
  the scrim guarantees, not against the average pixel.

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

**Split note:** this skill was separated from the general `accessibility` skill
so that the two do not duplicate each other in a flat skill namespace. General
interface accessibility lives there; media-specific accessibility lives here.

**Not sourced — written for this framework:** the event-photography alt-text
guidance, the "same photo may need different alt in different contexts" rule, the
manual testing sequence, and the detection commands. The alt decision table is
adapted from the W3C WAI decision tree cited above.
