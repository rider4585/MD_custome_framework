---
name: cross-device-testing
version: 1.0.0
description: |
  Verify a portfolio works on the devices the audience actually uses — a real
  device matrix, throttled conditions, orientation, and the automation that
  covers the rest. Use before launch, after a layout change, or when something
  works locally but not for the client.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## Cross-Device Testing

The developer's laptop is the least representative device in the audience. For a
Latur event company, the realistic visitor is on a **mid-range Android phone,
often over mobile data, frequently arriving from an Instagram in-app browser.**

That last one matters more than people expect and is almost never tested.

> **Before running anything:** get the client's real analytics if any exist, or
> their Instagram audience insights. A matrix built from assumptions tests the
> wrong things.

### Method

1. **Build the matrix from evidence**, not from a device-popularity article.
2. **Test the top three combinations manually**, on real hardware.
3. **Automate the rest** with Playwright across viewports.
4. **Test the in-app browsers** explicitly.
5. **Record what was tested and on what.**

### The matrix

Tier 1 gets manual testing on real hardware; tier 2 is automated; tier 3 is
best-effort.

| Tier | Device / context | Why |
|---|---|---|
| **1** | Mid-range Android (e.g. Redmi/Realme, 6.5", Chrome) | The modal visitor |
| **1** | **Instagram in-app browser, Android** | The likeliest arrival path from `@eventina.organisers` |
| **1** | iPhone, Safari | Higher-budget clients; Safari has distinct bugs |
| **2** | Android tablet / iPad, both orientations | |
| **2** | Desktop 1440 px, Chrome and Safari | The client reviews on this |
| **2** | Desktop 1920 px+ | Layout must not fall apart wide |
| **3** | Older Android WebView, Firefox, small 320 px | Best effort |

**Instagram's in-app browser is a real testing target**, not an edge case. It has
a reduced viewport (its own chrome eats vertical space), sometimes-stale WebView
versions, and it is where most social traffic will land. `100vh` bugs and sticky
headers fail here first — see `editorial-layout` on `svh`.

### Viewport sizes that matter

| Width | Represents |
|---|---|
| 320 px | Smallest realistic; WCAG SC 1.4.10 reflow target |
| 360–412 px | The bulk of Android |
| 390–430 px | Current iPhones |
| 768 px | Tablet portrait |
| 1024 px | Tablet landscape / small laptop |
| 1440 px | The client's laptop |
| 1920 px+ | Large desktop |

**Test at 320 px even though few use it** — it is the WCAG reflow requirement and
it surfaces overflow bugs that also affect 360 px.

### Automation

```js
// playwright.config.js
import { devices } from '@playwright/test';

export default {
  projects: [
    { name: 'android-mid',  use: { ...devices['Galaxy S9+'] } },
    { name: 'iphone',       use: { ...devices['iPhone 13'] } },
    { name: 'ipad',         use: { ...devices['iPad (gen 7)'] } },
    { name: 'desktop',      use: { ...devices['Desktop Chrome'],
                                   viewport: { width: 1440, height: 900 } } },
    { name: 'narrow',       use: { viewport: { width: 320, height: 640 } } },
  ],
};
```

```js
// overflow.spec.js — the single most valuable cross-device test
import { test, expect } from '@playwright/test';

for (const path of ['/', '/work/', '/work/sharma-wedding-2026/', '/contact/']) {
  test(`no horizontal overflow ${path}`, async ({ page }) => {
    await page.goto(path);
    const overflow = await page.evaluate(() =>
      document.documentElement.scrollWidth > document.documentElement.clientWidth
    );
    expect(overflow).toBe(false);
  });
}
```

**Horizontal overflow is the most common responsive bug and the easiest to
automate.** Run it on every page at every viewport; it costs seconds and catches
a class of bug that is otherwise found by the client.

### Throttling

Testing on a fast connection hides the problems that matter here.

```bash
# Chrome DevTools: Slow 4G + 4x CPU slowdown
# Lighthouse with mobile emulation and simulated throttling
npx lighthouse https://eventina.in/ --form-factor=mobile --throttling-method=simulate

# Real device, real network — Android remote debugging
adb devices && echo "open chrome://inspect on the desktop"
```

**Remote-debug a real Android phone at least once before launch.** Emulators do
not reproduce real CPU throttling, real GPU behaviour, or real font rendering.

### What breaks where

| Symptom | Usual cause |
|---|---|
| Hero taller than the screen on mobile | `100vh` — use `svh` → `editorial-layout` |
| Sticky header covers a focused field | Missing `scroll-margin-top` → `accessibility` |
| Horizontal scroll | A full-bleed element using `100vw` with a scrollbar present |
| Text zooms on input focus, iOS | Input `font-size` under 16 px |
| Video does not autoplay | Missing `playsinline`/`muted` → `video-on-web` |
| Devanagari renders incorrectly | Old Android WebView → `editorial-typography` |
| Tap does nothing | Target under 24 px, or a hover-only interaction |
| Fonts flash then reflow | Missing fallback metrics → `core-web-vitals` |

**Hover-only interactions do not exist on touch.** Anything revealed on hover
must also be reachable by tap and by keyboard.

### Detection

```bash
grep -rn '100vh' src/ --include=*.css --include=*.astro
grep -rn ':hover' src/ --include=*.css | grep -i 'display\|visibility\|opacity'
grep -rnE 'font-size:\s*1[0-5]px' src/ --include=*.css | grep -i input
grep -rn 'window.innerWidth\|navigator.userAgent' src/    # UA sniffing
npx playwright test --project=narrow
```

### Caveats

- **BrowserStack and emulators are not real devices.** They catch layout bugs;
  they miss performance and rendering ones.
- **The client's own device is the one that matters most politically.** Ask what
  they use and test it first — a bug they see personally outweighs ten they do
  not.
- **Instagram's WebView changes without notice.** Re-test after any major layout
  change.
- **Do not UA-sniff.** Feature-detect, and use media queries for layout.
- **Record the matrix in the launch notes.** "Tested on mobile" is not a record.

### Checklist

- [ ] Matrix built from real analytics or audience insights
- [ ] Tier 1 tested manually on real hardware
- [ ] Instagram in-app browser tested on Android
- [ ] iPhone Safari tested
- [ ] All key pages checked at 320, 360, 390, 768, 1024, 1440, 1920 px
- [ ] No horizontal overflow at any width, scrollbar present
- [ ] Automated overflow test in CI
- [ ] Throttled test at Slow 4G + 4× CPU
- [ ] One real Android device remote-debugged before launch
- [ ] No hover-only interactions
- [ ] Inputs ≥16 px to prevent iOS zoom
- [ ] Both orientations checked on tablet
- [ ] No UA sniffing
- [ ] Device matrix and results recorded

## References

- **Playwright — device emulation and project configuration** (Apache-2.0)
  <https://playwright.dev/docs/emulation>
- **WCAG 2.2 — SC 1.4.10 Reflow**, the 320 px requirement
  <https://www.w3.org/TR/WCAG22/#reflow>
- **MDN — viewport units `svh`/`lvh`/`dvh`** and mobile browser chrome
  <https://developer.mozilla.org/en-US/docs/Web/CSS/length#viewport-percentage_lengths>
- **Chrome for Developers — Remote debugging Android devices**
  <https://developer.chrome.com/docs/devtools/remote-debugging>
- **web.dev — "Interaction media features and their potential for detecting
  touch"**, the basis for avoiding hover-only interactions
  <https://web.dev/articles/interaction-media-features>

**Not sourced — written for this framework:** the tiered device matrix, the
Instagram in-app browser as a tier-1 target, the symptom/cause table, the
overflow test as the highest-value automation, and the detection commands.
