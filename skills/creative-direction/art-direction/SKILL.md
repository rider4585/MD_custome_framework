---
name: art-direction
version: 1.0.0
description: |
  Direct the visual language of a photo-led portfolio — image selection,
  sequencing, cropping, grading consistency, and the ratio of picture to space.
  Use when composing a case study or gallery, when a page "looks busy" or "looks
  cheap", or when choosing which twelve photos out of four hundred go on the site.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## Art Direction

On a portfolio site the images *are* the design. Layout, type, and colour are
scaffolding whose job is to not damage the photographs. Most premium-feeling
sites are ordinary layouts around ruthlessly selected pictures; most
cheap-feeling sites are ambitious layouts around whatever the client sent.

> **Before running anything:** load `emotional-brief` for this event type's
> register, and `photo-curation` for the culling procedure. This skill decides
> *how images are presented*; `photo-curation` decides *which survive*.

### Method

1. **Grade before you compose.** Inconsistent colour across a grid is the single
   most common reason a portfolio reads as amateur. Fix it first — no layout
   rescues a mismatched grid.
2. **Assign each image a role** (below). An image with no role is cut.
3. **Sequence for rhythm**, not chronology alone.
4. **Set the crop per role**, not per image.
5. **Audit the picture-to-space ratio** on every breakpoint.

### Image roles

Every published image is exactly one of these. The role sets its size, crop, and
position — which is what makes a grid feel directed rather than dumped.

| Role | Job | Count per case study | Treatment |
|---|---|---|---|
| **Establishing** | Where are we, how big is this | 1 | Full-bleed, widest crop, first |
| **Hero moment** | The emotional peak | 1 | Largest, breaks the grid, alone on its row |
| **Detail** | Proof of craft — flowers, place settings, lighting rig | 3–5 | Tight, square or portrait, may cluster |
| **Human** | Faces reacting, not posing | 2–4 | Portrait crop, mid-size |
| **Scale** | The crowd, the room full | 1–2 | Very wide, short height, full-bleed |
| **Closing** | Release — the exit, the last light | 1 | Wide, quiet, generous space after |

**Nine to fourteen images per case study.** Below nine there is no story; above
about fourteen the visitor starts scrolling rather than looking. This is a
framework opinion, not a research finding — but the failure mode of "upload the
whole album" is real and universal.

### Sequencing — rhythm over chronology

Chronological order is the default and it is usually flat, because real days have
long uneventful stretches. Sequence by **size alternation**:

```
WIDE (establishing)
  → two DETAILS side by side
    → HERO, full width, alone
      → HUMAN, HUMAN (pair)
        → WIDE (scale)
          → DETAIL, DETAIL, DETAIL (triptych)
            → CLOSING, wide, then whitespace
```

The rule underneath: **never place two same-size images adjacent unless they are
a deliberate pair**. Pairs read as comparison; accidental repetition reads as a
grid template.

### Crop discipline

- **Crop to the role, not to the frame you were given.** A 3:2 camera frame is an
  accident of hardware.
- **Never crop faces at the jaw or the top of the head** unless the whole
  sequence does it deliberately.
- **Respect the eyeline.** Subject looking left → space on the left.
- **Use `object-position` rather than re-exporting** when the same asset serves
  two aspect ratios, but check the focal point at every breakpoint — the default
  centre crop decapitates people on mobile more often than anything else.

```html
<!-- Art-directed crops: different image per viewport, not just different size -->
<picture>
  <source media="(min-width: 60rem)" srcset="/img/hero-wide.avif" type="image/avif">
  <source media="(min-width: 60rem)" srcset="/img/hero-wide.webp" type="image/webp">
  <source srcset="/img/hero-portrait.avif" type="image/avif">
  <img src="/img/hero-portrait.jpg" alt="" width="1200" height="1600" fetchpriority="high">
</picture>
```

Use `<picture>` with `media` when the *subject* should change between viewports;
use `srcset`/`sizes` alone when only the *resolution* changes. Confusing the two
is why hero images look wrong on phones. See `image-optimization`.

### Picture-to-space ratio

Density is the tell. A premium page gives images room; a budget page tiles them.

| Context | Target |
|---|---|
| Case-study body | 40–55% of viewport area is image at any scroll position |
| Portfolio index | Under 65%, with at least one full-width gutter per screen |
| Home hero | One image. Not a carousel of five |

**A carousel on a home hero is a decision not to decide.** It also costs LCP,
splits attention, and is routinely ignored by users. If the client insists,
argue once with the performance number from `core-web-vitals`, then comply and
make it excellent.

### Detection

```bash
# Images shipped without explicit dimensions — guaranteed layout shift
grep -rnE '<img(?![^>]*width=)' src/ --include=*.astro --include=*.html -P

# Decorative images that should have empty alt, and content images that must not
grep -rn 'alt=""' src/ --include=*.astro | head -20

# Art-directed sources present at all
grep -rn '<picture' src/ --include=*.astro | wc -l
```

### Caveats

- **You may be looking at a weak archive.** If the client's photographs are
  inconsistent, no art direction saves the site. Say it early, in writing, with
  examples — and price a shoot. Discovering this at launch is a failure of this
  skill, not of the photographer.
- **Grading has an ethical limit.** Do not alter skin tone, remove people, or
  composite events that did not happen. It is a portfolio, i.e. a factual claim.
- **The client's favourite photo is often the worst one.** Lose this argument
  gracefully; it is their business and their relationship with the family in it.

### Checklist

- [ ] All images graded to one consistent look before layout began
- [ ] Every published image assigned exactly one role
- [ ] 9–14 images per case study
- [ ] Sequence alternates size; no accidental same-size neighbours
- [ ] Crops set per role, eyelines respected, no jaw-line crops
- [ ] Art-directed `<picture>` where the subject changes by viewport
- [ ] Focal point checked at 375 px, 768 px, and 1440 px
- [ ] Every image has explicit `width`/`height`
- [ ] Picture-to-space ratio within target on all breakpoints
- [ ] Hero is a single image, not a carousel

## References

- **MDN — Responsive images**, on the distinct roles of `srcset`/`sizes` versus
  `<picture media>` for art direction
  <https://developer.mozilla.org/en-US/docs/Web/HTML/Guides/Responsive_images>
- **MDN — `object-fit` / `object-position`**
  <https://developer.mozilla.org/en-US/docs/Web/CSS/object-position>
- **web.dev — Optimize Largest Contentful Paint**, on `fetchpriority` and why the
  hero image governs perceived speed <https://web.dev/articles/optimize-lcp>
- **Nielsen Norman Group — auto-forwarding carousels are ignored and reduce task
  success** <https://www.nngroup.com/articles/designing-effective-carousels/>
- **WCAG 2.2 — SC 1.1.1 Non-text Content**, on decorative versus informative
  images <https://www.w3.org/TR/WCAG22/#non-text-content>

**Not sourced — written for this framework:** the six image roles and their
counts, the 9–14 rule, the size-alternation sequencing rule, the
picture-to-space ratio targets, and the detection commands.
