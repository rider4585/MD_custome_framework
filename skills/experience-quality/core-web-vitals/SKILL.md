---
name: core-web-vitals
version: 1.0.0
description: |
  Measure and fix LCP, INP, and CLS on an image-heavy site, on the devices the
  audience actually uses. Use when the site feels slow, before launch, or when
  PageSpeed Insights reports a failing metric.
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
---

## Core Web Vitals

Three metrics, and on a portfolio site **one of them dominates**: LCP, because
the largest contentful element is almost always a photograph.

Thresholds — measured at the **75th percentile** of real visits:

| Metric | Good | Needs work | Poor |
|---|---|---|---|
| **LCP** — Largest Contentful Paint | ≤ 2.5 s | ≤ 4.0 s | > 4.0 s |
| **INP** — Interaction to Next Paint | ≤ 200 ms | ≤ 500 ms | > 500 ms |
| **CLS** — Cumulative Layout Shift | ≤ 0.1 | ≤ 0.25 | > 0.25 |

> **Before running anything:** the target is a mid-range Android on Indian mobile
> data, not your laptop. A site that passes on a MacBook and fails on a
> ₹15,000 phone has failed.

### Method

1. **Measure in the field first** if there is traffic; lab data second.
2. **Identify the LCP element.** Do not guess.
3. **Fix in the order below** — the ordering reflects payoff.
4. **Re-measure on a throttled mid-range device.**
5. **Add a regression guard** to CI.

### Lab measurement

```bash
# Throttled, mobile, three runs — a single run is noise
npx lighthouse https://studio.example/ \
  --preset=desktop=false --form-factor=mobile \
  --throttling-method=simulate \
  --only-categories=performance \
  --output=json --output-path=./lh.json --quiet

# What was the LCP element?
jq -r '.audits["largest-contentful-paint-element"].details.items[0].items[0].node.snippet' lh.json

# What is blocking?
jq -r '.audits["render-blocking-resources"].details.items[]? | "\(.wastedMs)ms \(.url)"' lh.json
jq -r '.audits["unsized-images"].details.items[]?.url' lh.json
```

**Lighthouse's simulated throttling is optimistic.** For a real number, use
WebPageTest with a real device profile from an Indian location, or Chrome
DevTools with a 4× CPU slowdown and "Slow 4G".

### Field measurement

Lab data cannot tell you what real visitors experience. Once there is traffic:

- **PageSpeed Insights** shows CrUX field data for the origin, if it has enough
  traffic. A new portfolio site usually will not — expect "insufficient data" for
  months.
- **Collect your own** with the `web-vitals` library, sending to your analytics.

```js
import { onLCP, onINP, onCLS } from 'web-vitals';
const send = m => navigator.sendBeacon('/api/vitals',
  JSON.stringify({ name: m.name, value: m.value, id: m.id }));
onLCP(send); onINP(send); onCLS(send);
```

### LCP — the one that matters here

The LCP element on a portfolio is the hero image, near-universally.

**Fix in this order:**

1. **Is the LCP image lazy-loaded?** Remove it. `loading="lazy"` on the hero
   delays the request until layout — the single most common LCP mistake.
   → `image-optimization`
2. **Is it `fetchpriority="high"`?** Set it. The browser deprioritises images by
   default.
3. **Is it too large?** Target ≤150 kB. AVIF at quality 72–78.
4. **Is it discoverable in the initial HTML?** An image set by CSS
   `background-image`, or inside a hydrated island, is found late. Use a real
   `<img>` in the markup.
5. **Are fonts blocking it?** `font-display: swap`, preload the one face above
   the fold with `crossorigin`. → `editorial-typography`
6. **Is the HTML slow to arrive?** Check TTFB from India. A static site on a good
   CDN should be well under 300 ms. → `static-deploy`
7. **Is there a render-blocking stylesheet?** Inline critical CSS if so; on a
   portfolio the CSS should be small enough not to matter. → `css-architecture`

**Do not preload the hero if it is already an `<img>` with `fetchpriority="high"`
in the initial HTML.** Doing both can cause a double fetch.

### INP

INP is a JavaScript problem, and a static site should have very little.
Suspects, in order:

1. **A heavy island hydrating on interaction.** → `astro-islands`
2. **`scroll` event listeners** doing layout work. Use scroll-driven animations
   or `IntersectionObserver`. → `motion-design`
3. **A third-party script** — a raw YouTube iframe, a tag manager, a chat widget.
   Façade or remove. → `video-on-web`
4. **A large `client:load` island** competing for the main thread during load.

```bash
grep -rn "addEventListener('scroll'" src/ --include=*.js --include=*.astro
grep -rn 'client:load' src/ --include=*.astro
npm run build && ls -la dist/_astro/*.js | awk '{s+=$5} END {print s/1024, "kB JS"}'
```

### CLS

Almost entirely preventable, and on a portfolio almost entirely about images and
fonts.

| Cause | Fix |
|---|---|
| Images without dimensions | `width`/`height` or `aspect-ratio` on every image |
| Web font swap | `size-adjust` / `ascent-override` on the fallback face |
| Content injected above existing content | Reserve the space |
| Lightbox scroll lock | `scrollbar-gutter: stable` → `gallery-patterns` |
| Late-loading embeds | Reserve with `aspect-ratio` |

```bash
grep -rnP '<img(?![^>]*\b(width|height)=)' src/ --include=*.astro
grep -rn 'aspect-ratio' src/ --include=*.css | wc -l
```

### CI regression guard

```yaml
# .github/workflows/lighthouse.yml
- uses: treosh/lighthouse-ci-action@v12
  with:
    urls: |
      https://studio.example/
      https://studio.example/work/
    budgetPath: ./budget.json
    uploadArtifacts: true
```

Budgets live in `performance-budget`.

### Caveats

- **Lab ≠ field.** A perfect Lighthouse score with poor CrUX data means the lab
  conditions do not match your visitors.
- **Scores are not the goal.** A 100 that took a week is worse than a 92 and a
  better hero photograph. Optimise the metric, not the number.
- **CLS is measured over the whole session**, including interactions — a
  late-opening lightbox can hurt a page that scored zero at load.
- **INP replaced FID** as a Core Web Vital in March 2024. Guidance written before
  then optimises the wrong thing.
- **New sites have no field data.** Use lab data and be honest that it is a
  proxy.

### Checklist

- [ ] LCP element identified from a Lighthouse run, not guessed
- [ ] LCP image eager, `fetchpriority="high"`, ≤150 kB, a real `<img>` in the HTML
- [ ] No double-fetch from redundant preload
- [ ] Fonts `swap`; one preload with `crossorigin`
- [ ] TTFB from India measured and under ~300 ms
- [ ] Every image has dimensions or `aspect-ratio`
- [ ] Font fallback metrics adjusted
- [ ] No scroll-event layout work; scroll-driven animations used instead
- [ ] Third-party embeds façaded
- [ ] Measured on a throttled mid-range Android, three runs
- [ ] LCP ≤2.5 s, INP ≤200 ms, CLS ≤0.1 in that configuration
- [ ] CI budget guard in place
- [ ] Field collection wired up for after launch

## References

- **web.dev — Core Web Vitals** and the 75th-percentile threshold definitions
  <https://web.dev/articles/vitals>
- **web.dev — Optimize LCP**, priority hints, discoverability, and the
  lazy-loading trap <https://web.dev/articles/optimize-lcp>
- **web.dev — Interaction to Next Paint (INP)**, which replaced FID in March 2024
  <https://web.dev/articles/inp>
- **web.dev — Cumulative Layout Shift** <https://web.dev/articles/cls>
- **Chrome UX Report (CrUX)** — the field dataset behind PageSpeed Insights
  <https://developer.chrome.com/docs/crux>
- **Lighthouse** (Apache-2.0) <https://github.com/GoogleChrome/lighthouse>
- **`web-vitals` library** <https://github.com/GoogleChrome/web-vitals>
- **WebPageTest** — real-device, real-location testing
  <https://www.webpagetest.org/>

**Not sourced — written for this framework:** the seven-step LCP fix ordering,
the INP suspect list, the CLS cause/fix table as applied to galleries, the
mid-range-Android target, and the `jq` extraction commands.
