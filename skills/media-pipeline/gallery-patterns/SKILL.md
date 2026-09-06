---
name: gallery-patterns
version: 1.0.0
description: |
  Build image grids and lightboxes that are fast, keyboard-operable, and do not
  trap users — masonry, justified rows, lightbox behaviour, and when a carousel
  is the wrong answer. Use when building any gallery, or when a lightbox fails
  accessibility review.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## Gallery Patterns

The gallery is the core component of a portfolio and it is where accessibility
most often collapses — custom lightboxes with no keyboard support, focus escaping
to the page behind, and carousels that move while you are looking.

**Nearly all of this is a solved problem.** Use a maintained library or a native
element rather than hand-rolling; the hand-rolled version is what fails.

> **Before running anything:** load `image-optimization` for the loading strategy
> and `art-direction` for how images are grouped. This skill covers the
> *interaction*.

### Method

1. **Choose the grid form** from the trade-off table.
2. **Decide whether a lightbox is needed at all.**
3. **Use a maintained implementation**, or the native `<dialog>`.
4. **Test the whole flow with the keyboard only**, then with a screen reader.
5. **Verify no layout shift and no eager loading below the fold.**

### Grid forms

| Form | Pros | Cons | Use |
|---|---|---|---|
| **Fixed-ratio grid** | Predictable, zero CLS, trivial CSS | Crops every image | Portfolio index |
| **Justified rows** (Flickr-style) | Respects aspect ratios, no cropping | Needs dimensions up front; heavier | Mixed-ratio case-study galleries |
| **CSS columns masonry** | One line of CSS | **Reading order goes down columns, not across** — an accessibility and comprehension problem | Avoid where order matters |
| **Grid masonry** (`grid-template-rows: masonry`) | Correct order | Limited browser support; needs a fallback | Progressive enhancement only |
| **Carousel** | Compact | Hides content, poor discovery, routinely ignored | Only when space is genuinely fixed |

**Default to a fixed-ratio grid on the index and justified rows inside a case
study.** The index is for scanning; the case study is for looking.

```css
/* Fixed-ratio grid — no JS, no layout shift */
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(min(20rem, 100%), 1fr));
  gap: var(--space-s);
}
.grid img {
  width: 100%;
  aspect-ratio: 3 / 2;
  object-fit: cover;
  display: block;
}
```

**CSS `columns` masonry is a trap.** It reorders content down each column, so
keyboard and screen-reader order no longer matches the visual order — WCAG
SC 1.3.2 Meaningful Sequence. It looks right and reads wrong.

### Does it need a lightbox?

Often not. A lightbox adds a focus trap, keyboard handling, a close affordance,
history behaviour, and touch gestures — all of which can be got wrong.

Skip it when images are already large in the layout, or when the gallery is a
scroll-through story rather than a browse-and-inspect set.

Use one when the visitor genuinely needs detail (decor, table settings, a stage
build) that thumbnails cannot show.

### Lightbox requirements

If you build one, all of these are mandatory:

| Requirement | Why |
|---|---|
| `Esc` closes | Universal expectation; WCAG SC 2.1.2 No Keyboard Trap |
| Focus moves into the dialog on open | Otherwise keyboard users are stranded |
| **Focus is trapped inside while open** | Tabbing to the page behind is disorienting |
| Focus returns to the triggering thumbnail on close | Losing scroll position is the top complaint |
| Arrow keys navigate | Expected |
| Visible, labelled close button, ≥44×44 px | Touch target; WCAG SC 2.5.8 |
| `role="dialog"` + `aria-modal="true"` + accessible name | Announced correctly |
| Background scroll locked, without layout jump | Scrollbar removal causes a visible shift |
| Swipe on touch, without blocking vertical page scroll | Common bug |
| Full-size image lazy-loaded on open | Do not preload every full-size image |

Native `<dialog>` gives focus trapping, `Esc`, and the top layer for free:

```html
<dialog id="lightbox" aria-label="Photograph viewer">
  <button autofocus aria-label="Close viewer">✕</button>
  <img src="" alt="">
</dialog>
```

```js
const dlg = document.getElementById('lightbox');
let opener = null;

function open(thumb) {
  opener = thumb;
  dlg.querySelector('img').src = thumb.dataset.full;
  dlg.querySelector('img').alt = thumb.alt;
  dlg.showModal();                      // focus trap + Esc, from the platform
}
dlg.addEventListener('close', () => opener?.focus());   // return focus
```

`showModal()` — not `show()` — is what provides the trap and the backdrop.
Restoring focus to the opener is the one thing the platform does *not* do for
you, and skipping it is the most common remaining bug.

**Maintained alternative:** [PhotoSwipe](https://photoswipe.com/) (MIT) handles
touch gestures, pinch zoom, and history properly. Prefer it over a hand-rolled
lightbox with gesture support.

### Carousels

If a carousel is unavoidable:

- **Never auto-advance.** WCAG SC 2.2.2, and it is the single most disliked
  pattern in usability testing.
- Real `<button>` controls, keyboard-operable, ≥44 px.
- Follow the [ARIA APG carousel pattern](https://www.w3.org/WAI/ARIA/apg/patterns/carousel/).
- Do not hide off-screen slides from keyboard focus with `visibility` alone —
  use `inert` on inactive slides.
- [Embla Carousel](https://www.embla-carousel.com/) (MIT) is a reasonable base.

### Detection

```bash
grep -rn 'columns:' src/ --include=*.css | grep -i 'masonry\|gallery'   # column-order trap
grep -rn 'showModal\|show()' src/ --include=*.js                         # non-modal dialog
grep -rn 'setInterval' src/ --include=*.js | grep -i 'slide\|carousel'   # auto-advance
grep -rn 'overflow:\s*hidden' src/ --include=*.css | grep -i 'body'      # scroll-lock jump
grep -rn 'loading="lazy"' src/ --include=*.astro | wc -l                 # lazy used at all
```

Automate the rest — `axe-core` via Playwright catches missing dialog roles,
focus-order faults, and unlabelled controls. See `accessibility`.

### Caveats

- **`<dialog>` needs styling for the backdrop and for small screens.** The
  default UA styling is not sufficient; style `::backdrop`.
- **Scroll locking causes a horizontal jump** when the scrollbar disappears.
  Compensate with `scrollbar-gutter: stable`.
- **Masonry via `grid-template-rows: masonry` is still limited** — feature-detect
  with `@supports` and fall back to a fixed-ratio grid.
- **Do not lazy-load the first row.** On a tall phone, more is above the fold
  than you think.

### Checklist

- [ ] Grid form chosen against the trade-off table
- [ ] CSS `columns` masonry not used where reading order matters
- [ ] Lightbox justified, or omitted
- [ ] Native `<dialog>` + `showModal()`, or a maintained library
- [ ] `Esc` closes; focus enters on open and returns to the opener on close
- [ ] Focus trapped while open; background `inert` or dialog-managed
- [ ] Arrow-key navigation; close button ≥44 px and labelled
- [ ] Scroll locked without layout jump (`scrollbar-gutter: stable`)
- [ ] Touch swipe does not block vertical scrolling
- [ ] Full-size images loaded on demand
- [ ] No auto-advancing carousel anywhere
- [ ] Whole flow tested with keyboard only, then with a screen reader
- [ ] `aspect-ratio` set on every tile; CLS measured at zero

## References

- **WAI-ARIA Authoring Practices Guide — Dialog (Modal) pattern and Carousel
  pattern** <https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal/>
- **MDN — `<dialog>`, `showModal()`, `::backdrop`, and the `inert` attribute**
  <https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/dialog>
- **WCAG 2.2 — SC 1.3.2 Meaningful Sequence, SC 2.1.2 No Keyboard Trap,
  SC 2.2.2 Pause Stop Hide, SC 2.5.8 Target Size (Minimum)**
  <https://www.w3.org/TR/WCAG22/#meaningful-sequence>
- **Inclusive Components — Heydon Pickering**, on galleries, modals, and
  focus management <https://inclusive-components.design/>
- **Nielsen Norman Group — carousel usability findings**
  <https://www.nngroup.com/articles/designing-effective-carousels/>
- **PhotoSwipe** (MIT) <https://photoswipe.com/> and **Embla Carousel** (MIT)
  <https://www.embla-carousel.com/>
- **MDN — CSS masonry layout status and `scrollbar-gutter`**
  <https://developer.mozilla.org/en-US/docs/Web/CSS/scrollbar-gutter>

**Not sourced — written for this framework:** the grid-form trade-off table, the
default of fixed-ratio index plus justified case study, the
does-it-need-a-lightbox test, the consolidated lightbox requirements table, and
the detection commands.
