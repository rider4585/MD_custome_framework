---
name: image-optimization
version: 1.0.0
description: |
  Ship a photograph-heavy site that loads fast on a mid-range Android over mobile
  data — formats, responsive sizing, lazy loading, priority hints, and placeholder
  strategy. Use whenever adding images, when LCP is slow, or when the page weight
  is over budget.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## Image Optimization

A portfolio site is 90% images by weight. Everything else — fonts, CSS, JS — is
rounding error. **If images are handled well the site is fast, and if they are
not, nothing else you do will save it.**

The target audience matters: a mid-range Android phone on Indian mobile data,
not a laptop on office wifi.

> **Before running anything:** load `asset-workflow` for where source files live
> and how they are named. Optimising the wrong master is wasted work.

### Method

1. **Set a page budget** and hold it (below).
2. **Transcode at build time**, never at request time and never by hand.
3. **Generate a width ladder** and describe layout with `sizes`.
4. **Prioritise the LCP image; lazy-load the rest.**
5. **Measure on a throttled mid-range device**, not in your browser.

### Budgets

| Page | Total transferred | Largest single image |
|---|---|---|
| Home | ≤ 900 kB | ≤ 200 kB |
| Portfolio index | ≤ 1.2 MB above the fold | ≤ 120 kB per thumbnail |
| Case study | ≤ 1.5 MB above the fold | ≤ 250 kB hero |
| Any page, LCP image | — | **≤ 150 kB** |

Below-the-fold images are lazy-loaded and do not count against the above-fold
budget — but the *total* page weight still matters on metered data. Full
treatment in `performance-budget`.

### Formats

| Format | Use | Notes |
|---|---|---|
| **AVIF** | First choice for photographs | 30–50% smaller than WebP at similar quality; slower to encode, which is fine at build time |
| **WebP** | Fallback | Universally supported in practice |
| **JPEG** | Final fallback | Only if you support genuinely ancient clients |
| **PNG** | Never for photographs | Logos and hard-edged graphics only |
| **SVG** | Logos, icons | Sanitise if user-supplied |

Emit AVIF and WebP, let the browser choose:

```astro
---
import { Picture } from 'astro:assets';
import hero from '../assets/events/sharma-wedding/hero.jpg';
---
<Picture
  src={hero}
  formats={['avif', 'webp']}
  fallbackFormat="jpeg"
  widths={[400, 800, 1200, 1600, 2000]}
  sizes="(min-width: 75rem) 1200px, (min-width: 48rem) 90vw, 100vw"
  alt="The bride's family greeting guests at the mandap entrance"
  loading="eager"
  fetchpriority="high"
  quality={72}
/>
```

Astro uses **sharp** (Apache-2.0), which wraps **libvips** — the reason build-time
transcoding of hundreds of event photographs is feasible at all.

### Quality settings

Quality 72–78 for AVIF and WebP is visually indistinguishable from 90 on
photographs and roughly half the bytes. Quality 85+ on a photograph is almost
always wasted.

**Exception: images with large flat areas or gradients** — a dark reception shot,
a sky — band at low quality. Check those individually in
[Squoosh](https://squoosh.app/) and raise only those.

### `sizes` — the most-often-wrong attribute

`sizes` tells the browser how wide the image will *render*, so it can pick from
`srcset` **before CSS is parsed**. Get it wrong and the browser downloads a
2000 px image for a 400 px slot.

```html
<!-- WRONG: default is 100vw, so a 3-column grid still downloads full-width -->
<img srcset="..." alt="...">

<!-- RIGHT: describes the actual rendered width at each breakpoint -->
<img srcset="..."
     sizes="(min-width: 75rem) 380px, (min-width: 48rem) 45vw, 100vw"
     alt="...">
```

**`sizes` must match the CSS.** When the layout changes, `sizes` changes. This is
the most common silent performance regression on portfolio sites — nothing
breaks, the page just gets slower.

### Loading priority

| Image | `loading` | `fetchpriority` | `decoding` |
|---|---|---|---|
| LCP / hero | `eager` | `high` | `sync` |
| Above the fold, not LCP | `eager` | *(default)* | `async` |
| Below the fold | `lazy` | *(default)* | `async` |

**Never `loading="lazy"` on the LCP image.** It delays the request until layout,
which measurably worsens LCP — one of the most common and most costly mistakes.

**Never lazy-load anything in the first viewport**, and be careful with the first
row of a grid: on a tall phone, the "second row" is often visible.

### Layout stability

Always set `width` and `height` (or `aspect-ratio`). Without them the page
reflows as each image arrives, which is a CLS failure and feels broken.

```css
.gallery img {
  width: 100%;
  height: auto;          /* with width/height attrs present, no shift */
  aspect-ratio: 3 / 2;
  object-fit: cover;
}
```

Astro's `<Image>`/`<Picture>` infer dimensions from local imports automatically.
**Remote images do not get this** — pass `width` and `height` explicitly.

### Placeholders

| Approach | Cost | Use |
|---|---|---|
| Dominant colour | ~0 | **Default.** One `background-color` from the image |
| LQIP / blurred base64 | 200–800 B each | Hero images only |
| Skeleton shimmer | CSS | Avoid — animated, and reads as a loading app, not a gallery |
| Nothing | 0 | Fine below the fold with correct dimensions reserved |

Dominant colour is the right default: near-free, no extra request, and it makes
a grid feel composed while loading.

### Detection

```bash
# LCP candidates wrongly lazy-loaded
grep -rn 'loading="lazy"' src/ --include=*.astro | grep -i 'hero\|banner'

# Missing sizes on responsive images
grep -rn 'srcset' src/ --include=*.astro --include=*.html | grep -v 'sizes='

# Raw <img> bypassing the build pipeline
grep -rn '<img ' src/ --include=*.astro | grep -v 'astro:assets'

# Missing dimensions
grep -rnP '<img(?![^>]*\bwidth=)' src/ --include=*.astro

# Oversized source files reaching the repo
find src/assets public -type f \( -name '*.jpg' -o -name '*.png' \) -size +2M

# What actually shipped
du -sh dist/_astro/*.{avif,webp,jpg} 2>/dev/null | sort -h | tail -20
```

### Caveats

- **Build time grows with image count.** Hundreds of AVIF variants can push a
  build past a CI timeout. Cache the transform output, or reduce the width ladder
  — five widths is usually plenty.
- **AVIF encoding is slow but decoding is fine.** Do not let build time push you
  back to JPEG.
- **`quality` is per-image in reality.** The global setting is a starting point.
- **Do not upscale.** Serving a 2000 px variant of a 1200 px master wastes bytes
  and looks soft.
- **Strip EXIF before publishing** — event photos carry GPS. See `asset-workflow`.

### Checklist

- [ ] Page and per-image budgets set and measured
- [ ] All images processed at build time through the framework pipeline
- [ ] AVIF + WebP emitted with a sensible fallback
- [ ] Quality 72–78, with flat/gradient images checked individually
- [ ] Width ladder covers real rendered sizes; no upscaling
- [ ] `sizes` matches the CSS at every breakpoint
- [ ] LCP image `eager` + `fetchpriority="high"`, never lazy
- [ ] Everything below the fold lazy, including a tall phone's second row
- [ ] `width`/`height` or `aspect-ratio` on every image
- [ ] Dominant-colour placeholders; LQIP only on heroes
- [ ] EXIF stripped
- [ ] Measured on a throttled mid-range Android, not a laptop

## References

- **web.dev — Optimize Largest Contentful Paint**, on priority hints, lazy-loading
  the LCP image, and preloading <https://web.dev/articles/optimize-lcp>
- **web.dev — Cumulative Layout Shift** and reserving image dimensions
  <https://web.dev/articles/cls>
- **MDN — Responsive images**, `srcset`, `sizes`, and `<picture>`
  <https://developer.mozilla.org/en-US/docs/Web/HTML/Guides/Responsive_images>
- **MDN — `fetchpriority`, `loading`, `decoding`**
  <https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/img#fetchpriority>
- **Astro docs — Images**, `<Image>`, `<Picture>`, `getImage()`, and the sharp
  service <https://docs.astro.build/en/guides/images/>
- **sharp** (Apache-2.0) and **libvips** (LGPL-2.1), the transcoding engine
  <https://github.com/lovell/sharp>
- **Squoosh** (Apache-2.0) for per-image manual tuning
  <https://github.com/GoogleChromeLabs/squoosh>

**Not sourced — written for this framework:** the page and per-image budget
tables, the 72–78 quality recommendation, the placeholder comparison, the
dominant-colour default, and the detection commands.
