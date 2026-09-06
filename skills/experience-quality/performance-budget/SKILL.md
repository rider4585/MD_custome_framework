---
name: performance-budget
version: 1.0.0
description: |
  Set, enforce, and defend byte and timing budgets so an image-heavy site does
  not degrade one commit at a time. Use when starting a project, when a new
  dependency or hero video is proposed, or when the site has quietly got slower.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## Performance Budget

Sites do not become slow in one commit. They become slow through twenty
reasonable-looking additions, each defensible on its own. **A budget is what
makes the twenty-first addition a decision rather than a default.**

The budget's real job is not measurement — it is giving you a number to point at
when someone asks for a 12 MB hero video.

> **Before running anything:** agree the budget with the client *before* they ask
> for the video. A budget introduced as a refusal is an argument; a budget agreed
> at kickoff is a constraint.

### Method

1. **Set budgets from the audience**, not from a generic template.
2. **Write them into CI** so they are enforced, not aspirational.
3. **Attribute every overage** to a specific asset.
4. **Trade, do not stack** — anything new displaces something.
5. **Review after every content batch**, since content is what grows.

### The budgets

Derived from the target: a mid-range Android on Indian mobile data, aiming for
LCP ≤ 2.5 s.

| Resource | Budget | Notes |
|---|---|---|
| **HTML** | ≤ 25 kB | Compressed |
| **CSS** | ≤ 40 kB | Whole site → `css-architecture` |
| **JavaScript** | ≤ 60 kB per page | **0 kB** on non-interactive pages → `astro-islands` |
| **Fonts** | ≤ 150 kB | Latin + Devanagari, subset → `editorial-typography` |
| **LCP image** | ≤ 150 kB | → `image-optimization` |
| **Above-fold total** | ≤ 900 kB | Home |
| **Above-fold total** | ≤ 1.5 MB | Case study |
| **Hero video** | ≤ 1.5 MB, ≤ 8 s | If used at all → `video-on-web` |
| **Requests, above fold** | ≤ 30 | |
| **Third-party scripts** | **0** | Cookieless analytics only → `static-deploy` |

**Third-party budget of zero is deliberate.** Every third-party script is bytes
you do not control, a privacy question under DPDP, and a dependency that can slow
down without warning. Cookieless analytics is the one permitted exception, and it
is under 2 kB.

### Enforcing in CI

```json
// budget.json — Lighthouse CI resource budgets
[{
  "path": "/*",
  "resourceSizes": [
    { "resourceType": "document",   "budget": 25 },
    { "resourceType": "stylesheet", "budget": 40 },
    { "resourceType": "script",     "budget": 60 },
    { "resourceType": "font",       "budget": 150 },
    { "resourceType": "image",      "budget": 900 },
    { "resourceType": "third-party","budget": 5 },
    { "resourceType": "total",      "budget": 1200 }
  ],
  "resourceCounts": [
    { "resourceType": "third-party", "budget": 2 },
    { "resourceType": "total",       "budget": 30 }
  ],
  "timings": [
    { "metric": "largest-contentful-paint", "budget": 2500 },
    { "metric": "cumulative-layout-shift",  "budget": 0.1  },
    { "metric": "interactive",              "budget": 3500 }
  ]
}]
```

**Fail the build, do not warn.** A warning is read once and then ignored; the
whole value of a budget is that crossing it stops something.

### A cheap local check

Not every project wants Lighthouse CI. This catches most regressions:

```bash
#!/usr/bin/env bash
# bin/check-budget.sh — run after npm run build
set -euo pipefail
cd dist

kb() { du -bc $1 2>/dev/null | tail -1 | awk '{printf "%.0f", $1/1024}'; }

js=$(kb '_astro/*.js'); css=$(kb '_astro/*.css'); fonts=$(kb 'fonts/*')
echo "JS: ${js} kB   CSS: ${css} kB   Fonts: ${fonts} kB"

fail=0
(( js    > 60  )) && { echo "✗ JS over budget (${js} > 60 kB)"; fail=1; }
(( css   > 40  )) && { echo "✗ CSS over budget (${css} > 40 kB)"; fail=1; }
(( fonts > 150 )) && { echo "✗ Fonts over budget (${fonts} > 150 kB)"; fail=1; }

echo "Largest images:"
find . -name '*.avif' -o -name '*.webp' -o -name '*.jpg' \
  | xargs du -k 2>/dev/null | sort -rn | head -5 \
  | awk '{ printf "  %s kB  %s\n", $1, $2; if ($1 > 150) exit_code=1 }'

exit $fail
```

### Attribution

When a budget is breached, name the asset. "The page got heavier" is not
actionable.

```bash
npm run build
du -k dist/_astro/*.js | sort -rn | head -10
npx source-map-explorer 'dist/_astro/*.js'        # what is inside the bundle
find dist -name '*.avif' -size +150k              # oversized images
diff <(git show main:budget-report.txt) budget-report.txt   # what changed
```

### Trading, not stacking

The rule that makes a budget survive contact with a client: **a new asset must
displace an equivalent one.**

- Want a hero video? It replaces the hero image's budget, and the video *is* the
  above-fold budget.
- Want a chat widget? It uses the entire third-party budget. What is being
  removed?
- Want a React island? That is most of the JS budget on that page.

**Show the trade in the same sentence as the cost.** "We can add the video; the
home page then loads in about 4 seconds instead of 2 on a phone" is a decision
the client can make. "That would exceed our performance budget" is not.

### Caveats

- **Budgets must be realistic or they get disabled.** A budget that fails every
  build teaches the team to bypass CI, which is worse than no budget.
- **Content growth is the usual cause of drift**, not code. Re-check after each
  batch of case studies.
- **Compressed vs uncompressed confusion** causes false alarms — Lighthouse
  reports transfer size; `du` reports disk size. Be consistent.
- **Budgets are per page, not per site.** The home page and a case study have
  different profiles.
- **A budget is a proxy for experience.** If the metrics pass and the site feels
  slow, believe the feeling and investigate.

### Checklist

- [ ] Budgets set from the real audience's device and network
- [ ] Agreed with the client at kickoff, before the first big ask
- [ ] Written into CI and **failing** the build, not warning
- [ ] Per-page, not per-site
- [ ] Zero third-party scripts, with the analytics exception documented
- [ ] Attribution tooling available so overages name an asset
- [ ] Trade rule established: additions displace, not stack
- [ ] Re-checked after every content batch
- [ ] Transfer-vs-disk size consistently used

## References

- **web.dev — "Performance budgets 101"** and the budget-as-decision-tool framing
  <https://web.dev/articles/performance-budgets-101>
- **Lighthouse CI — budgets.json format**, `resourceSizes`, `resourceCounts`,
  `timings` <https://github.com/GoogleChrome/lighthouse-ci/blob/main/docs/configuration.md>
- **web.dev — Core Web Vitals thresholds**, which the timing budgets target
  <https://web.dev/articles/vitals>
- **source-map-explorer** — bundle attribution
  <https://github.com/danvk/source-map-explorer>
- **`core-web-vitals`, `image-optimization`, `astro-islands`** (this framework) —
  where each budget line is spent

**Not sourced — written for this framework:** the specific budget values, the
zero-third-party position, the trade-don't-stack rule and how to phrase it to a
client, the local check script, and the realistic-or-disabled caveat.
