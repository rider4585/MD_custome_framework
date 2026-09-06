---
name: editorial-layout
version: 1.0.0
description: |
  Compose asymmetric, magazine-style page layouts that carry a portfolio without
  looking like a template — grid strategy, full-bleed breakouts, whitespace as a
  structural element, and vertical rhythm. Use when building any page of a
  portfolio site, or when a layout is technically fine but reads as generic.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## Editorial Layout

The default web layout — centred column, equal cards, uniform gaps — is a
solved, safe, and instantly recognisable *template*. A premium portfolio needs
the opposite: a layout with a point of view, where the page composition itself
suggests the company makes considered choices.

The technique is **one grid, deliberately broken**. Not many grids, and not no
grid.

> **Before running anything:** load `art-direction` for image roles — layout
> follows role assignment, never the reverse.

### Method

1. **Define one grid** for the whole site. Everything aligns to it.
2. **Choose the breakout mechanism** — how elements escape the content column.
3. **Set whitespace as a scale**, not as ad-hoc margins.
4. **Establish vertical rhythm** so scrolling has cadence.
5. **Verify at three widths** that asymmetry still reads as intent, not as bug.

### One grid, broken deliberately

The full-bleed grid: a named-line CSS Grid where content sits in a centre track
and anything can escape to a wider track or the full viewport. This is the
single most useful layout primitive for a portfolio.

```css
.page {
  display: grid;
  grid-template-columns:
    [full-start] minmax(var(--gutter), 1fr)
    [wide-start] minmax(0, 12rem)
    [content-start] min(100% - (var(--gutter) * 2), var(--measure)) [content-end]
    minmax(0, 12rem) [wide-end]
    minmax(var(--gutter), 1fr) [full-end];
}

/* Default: everything sits in the readable column */
.page > * { grid-column: content; }

/* Opt out, per element */
.breakout-wide  { grid-column: wide; }
.breakout-full  { grid-column: full; }
```

`min(100% - gutter*2, --measure)` keeps the text column at a readable measure
while guaranteeing the gutter on small screens — no media query needed.

**Why named lines rather than nested containers:** a full-bleed image inside a
centred wrapper requires the `100vw` margin hack, which breaks when a scrollbar
is present and causes horizontal overflow. Named grid lines have no such failure.

### Asymmetry that reads as intent

Random offsets read as bugs. Asymmetry reads as design when it is **consistent
and repeated**:

- Pick **one** side for offsets and keep it. Alternating left/right on every
  section reads as a template again.
- Offsets should be **large** — a 30–40% shift. A 6% offset looks like a mistake.
- **Anchor asymmetric elements to a grid line.** An image that starts at
  `wide-start` while text starts at `content-start` is deliberate; an image at
  `margin-left: 37px` is not.
- Give asymmetric compositions **more** vertical space, not less. Crowding
  destroys the effect.

### Whitespace as a scale

Whitespace is the most reliable premium signal and the first thing cut under
pressure. Make it a token so it cannot be nibbled away.

```css
:root {
  /* Fluid space scale — see Utopia. Each step ~1.5x */
  --space-3xs: clamp(0.25rem, 0.23rem + 0.11vw, 0.31rem);
  --space-xs:  clamp(0.5rem,  0.46rem + 0.22vw, 0.63rem);
  --space-s:   clamp(1rem,    0.91rem + 0.43vw, 1.25rem);
  --space-m:   clamp(1.5rem,  1.37rem + 0.65vw, 1.88rem);
  --space-l:   clamp(2rem,    1.83rem + 0.87vw, 2.5rem);
  --space-xl:  clamp(3rem,    2.74rem + 1.30vw, 3.75rem);
  --space-2xl: clamp(4rem,    3.65rem + 1.74vw, 5rem);
  --space-3xl: clamp(6rem,    5.48rem + 2.61vw, 7.5rem);
}
```

**Section spacing on a portfolio should feel excessive on a desktop mock and
correct in the browser.** Use `--space-3xl` between major sections; designers
consistently under-space in static tools because the whole canvas is visible at
once.

### Vertical rhythm

Scrolling a case study should have cadence: dense, open, dense, open. Flat rhythm
is why long pages feel like a chore.

| Zone | Height guidance | Space after |
|---|---|---|
| Hero | 70–85 vh — **never 100 vh**, leave a scroll cue | `--space-3xl` |
| Text block | Natural, max `--measure` wide | `--space-2xl` |
| Image pair | Equal heights, small gap between | `--space-2xl` |
| Full-bleed | 60–80 vh | `--space-3xl` |
| Quote / testimonial | Short, generously spaced | `--space-3xl` |

**Never a 100 vh hero.** It hides the fact that the page continues, and on mobile
the dynamic browser chrome makes `100vh` overflow. Use `min-height: 80svh` — the
small-viewport unit is stable while the URL bar animates.

### Detection

```bash
# The 100vh hero problem
grep -rnE '100vh' src/ --include=*.astro --include=*.css

# Hard-coded spacing that bypasses the scale
grep -rnE '(margin|padding|gap):[^;]*[0-9]+(px|rem)' src/ --include=*.css | grep -v 'var(--space'

# The full-bleed margin hack, which the named-line grid replaces
grep -rnE 'margin-(left|inline).*-50vw|width:\s*100vw' src/ --include=*.css
```

### Caveats

- **Asymmetry costs responsive effort.** Every breakout needs checking at every
  breakpoint. Budget for it or use fewer, bigger breakouts.
- **Editorial layout can hurt scanning.** A visitor comparing three organisers is
  skimming. Keep navigation, services, and contact conventional; save the
  composition for the work itself.
- **`svh`/`dvh` need a fallback** for older browsers — declare `vh` first, then
  `svh`.
- **RTL is not a concern here, but Devanagari line height is.** Devanagari needs
  more leading than Latin at the same size; see `multilingual-content`.

### Checklist

- [ ] One named-line grid defined site-wide
- [ ] Breakouts use grid lines, not `100vw` margin hacks
- [ ] Asymmetry consistent in direction and large enough to read as intent
- [ ] Space scale tokenised; no raw spacing values in components
- [ ] Section spacing at `--space-3xl`, verified in browser not in mock
- [ ] Hero ≤ 85 vh using `svh` with a `vh` fallback
- [ ] Vertical rhythm alternates dense and open
- [ ] Text measure constrained to 60–75 characters
- [ ] No horizontal overflow at 320 px — checked with a scrollbar present
- [ ] Layout verified at 375, 768, 1024, and 1440 px

## References

- **MDN — CSS Grid named grid lines and `grid-template-columns`**
  <https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_grid_layout>
- **Utopia — fluid space and type scales via `clamp()`**, the source of the space
  scale approach used above <https://utopia.fyi/space/calculator/>
- **Smashing Magazine — "Designing And Building With Fluid Type And Space
  Scales"** (Wilcox & Bell, 2021), the Utopia method explained
  <https://www.smashingmagazine.com/2021/04/designing-developing-fluid-type-space-scales/>
- **MDN — viewport units `svh`, `lvh`, `dvh`** and the mobile URL-bar problem
  <https://developer.mozilla.org/en-US/docs/Web/CSS/length>
- **WCAG 2.2 — SC 1.4.10 Reflow**, no horizontal scrolling at 320 px equivalent
  <https://www.w3.org/TR/WCAG22/#reflow>

**Not sourced — written for this framework:** the full-bleed grid track
definition as written, the asymmetry-reads-as-intent rules, the vertical-rhythm
table, the "excessive in the mock, correct in the browser" heuristic, and the
detection commands.
