---
name: editorial-typography
version: 1.0.0
description: |
  Build a display-led type system for a portfolio — pairing, fluid scale, optical
  sizing, and Devanagari support alongside Latin. Use when setting up typography
  for a portfolio or marketing site, when headlines look weak at large sizes, or
  when adding <the local language> or Hindi text to a Latin-first design.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## Editorial Typography

Interface typography optimises for scanning small text. **Editorial typography
optimises for one enormous headline and a small amount of very readable prose.**
The rules differ enough that applying UI type guidance to a portfolio produces
timid, forgettable pages.

> **Before running anything:** confirm from `project-client-brand` whether the
> site ships <the local language> or Hindi content. Devanagari support changes the font
> selection entirely and cannot be retrofitted cheaply.

### Method

1. **Pick the display face first.** It carries the brand; the body face supports
   it.
2. **Generate a fluid scale** between two viewport poles rather than picking
   breakpoint sizes.
3. **Set optical corrections** for large sizes — tracking, leading, alignment.
4. **Add the Devanagari stack** with matched vertical metrics, if needed.
5. **Self-host and subset**, then measure the font payload.

### Pairing

Two families. A third needs a written justification.

| Role | Requirements | Open-licence examples |
|---|---|---|
| **Display** | Distinctive at 4 rem+, real weight range, good at tight tracking | Fraunces, Instrument Serif, Bricolage Grotesque, Playfair Display |
| **Body** | Invisible, high x-height, excellent at 16–20 px, tabular figures | Instrument Sans, Inter, Source Sans 3, IBM Plex Sans |

Pairing that works: **high contrast in category, low contrast in mood.** A
high-contrast serif display with a neutral grotesque body reads as considered. A
serif with another serif of similar era reads as indecision.

**Test the display face at its real size before committing.** Faces chosen from a
specimen at 24 px frequently fall apart at 96 px — weak joins, awkward apertures,
uneven colour. This is the single most common typography mistake on portfolio
sites.

### Fluid scale

Two poles, browser interpolates. This is the Utopia method.

```css
:root {
  --step--1: clamp(0.83rem, 0.80rem + 0.16vw, 0.94rem);
  --step-0:  clamp(1.00rem, 0.95rem + 0.22vw, 1.13rem);  /* body */
  --step-1:  clamp(1.20rem, 1.13rem + 0.33vw, 1.41rem);
  --step-2:  clamp(1.44rem, 1.34rem + 0.49vw, 1.76rem);
  --step-3:  clamp(1.73rem, 1.58rem + 0.71vw, 2.20rem);
  --step-4:  clamp(2.07rem, 1.87rem + 1.01vw, 2.75rem);
  --step-5:  clamp(2.49rem, 2.20rem + 1.42vw, 3.43rem);
  --step-6:  clamp(2.99rem, 2.59rem + 1.97vw, 4.29rem);  /* display */
}
```

**Never `clamp()` without a `rem`-based middle term.** `clamp(1rem, 4vw, 3rem)`
ignores the user's browser font-size setting at most viewports — a WCAG 1.4.4
failure. The `rem + vw` form scales with both.

### Optical corrections at display size

Type set at 4 rem needs different treatment from type at 1 rem. Applying body
defaults to a headline is why headlines look loose and weak.

| Size | Tracking | Line height | Notes |
|---|---|---|---|
| `--step-6` display | `-0.03em` to `-0.02em` | 0.95–1.05 | Tighten hard; default spacing looks accidental |
| `--step-4/5` heading | `-0.015em` | 1.1–1.2 | |
| `--step-1/2` subhead | `0` | 1.3 | |
| `--step-0` body | `0` | 1.5–1.6 | 1.6 for Devanagari |
| `--step--1` caption | `+0.01em` | 1.4 | Small text needs *more* tracking |

```css
.display {
  font-size: var(--step-6);
  line-height: 1.0;
  letter-spacing: -0.025em;
  text-wrap: balance;          /* prevents a one-word last line */
  font-optical-sizing: auto;   /* variable fonts with an opsz axis */
}

p { text-wrap: pretty; }       /* avoids orphans without reflowing headlines */
```

`text-wrap: balance` on headings and `pretty` on paragraphs remove the two most
visible ragging faults; both degrade silently where unsupported.

### Devanagari alongside Latin

<the local language> is written in Devanagari, and it is **not** a drop-in substitution.

- **Devanagari needs more vertical space.** The shirorekha (top bar) plus
  above/below marks mean matras collide at Latin leading. Add 0.1–0.2 to line
  height for Devanagari text.
- **Apparent size differs at the same `font-size`.** Devanagari typically looks
  smaller; use `size-adjust` in `@font-face` or bump the Devanagari step by one.
- **Set `lang`** so the browser picks correct fonts, hyphenation, and so screen
  readers switch voice: `<p lang="mr">`. This is WCAG SC 3.1.2.

```css
@font-face {
  font-family: 'Mukta';
  src: url('/fonts/mukta-devanagari-400.woff2') format('woff2');
  unicode-range: U+0900-097F, U+A8E0-A8FF, U+1CD0-1CF9;  /* Devanagari only */
  font-display: swap;
}

:root {
  --font-body: 'Instrument Sans', 'Mukta', system-ui, sans-serif;
  --font-display: 'Fraunces', 'Tiro Devanagari <the local language>', Georgia, serif;
}

:lang(mr), :lang(hi) { line-height: 1.7; }
```

The `unicode-range` split means Latin readers never download the Devanagari file
and vice versa — the browser fetches only what the page actually uses.

**Recommended open-licence Devanagari faces** (all SIL OFL 1.1):

| Face | Use | Note |
|---|---|---|
| [Tiro Devanagari <the local language>](https://fonts.google.com/specimen/Tiro+Devanagari+<the local language>) | Display / literary | Drawn for <the local language> specifically, has an italic |
| [Mukta](https://fonts.google.com/specimen/Mukta) | UI and body | Humanist, 7 weights |
| [Noto Sans Devanagari](https://fonts.google.com/noto/specimen/Noto+Sans+Devanagari) | Fallback | Widest coverage |

<the local language> uses some conventions that differ from Hindi (e.g. the eyelash *ḷa*).
**Have a native reader check rendered output** — no automated check catches this.

### Loading

- **Self-host** via Fontsource or your own `woff2`. No third-party request, no
  privacy exposure, no extra connection on the critical path.
- **Variable fonts** where a real weight range is used; two static weights beat a
  variable font whose extra axes are unused.
- **Subset aggressively** — `unicode-range` per script, and drop unused features.
- `font-display: swap`, and **preload only the display face** used above the
  fold.
- **Budget: under 150 kB total fonts.** Latin variable + Devanagari fits.

```html
<link rel="preload" href="/fonts/fraunces-variable.woff2" as="font"
      type="font/woff2" crossorigin>
```

`crossorigin` is required on font preloads even same-origin — omitting it causes
a *double* download, which is a common silent regression.

### Detection

```bash
grep -rnE 'font-size:\s*[0-9.]+px' src/ --include=*.css          # px sizing
grep -rnE 'clamp\([^)]*vw[^)]*\)' src/ --include=*.css | grep -v rem  # vw-only clamp
grep -rn 'fonts.googleapis.com' src/                              # not self-hosted
grep -rn 'rel="preload"' src/ | grep 'as="font"' | grep -v crossorigin
grep -rn 'lang=' src/ --include=*.astro | head                    # lang on <the local language> blocks
du -ch public/fonts/*.woff2 2>/dev/null | tail -1                 # font budget
```

### Caveats

- **Display faces are a taste decision the client owns.** Present two, in situ,
  over their own photographs — never as a specimen sheet.
- **`text-wrap: balance` has a line-count limit** in browsers and silently stops
  applying on long blocks. It is for headings.
- **Never rely on font-based iconography.** It fails with font-blocking and
  reads as garbage to screen readers.
- **Devanagari rendering differs across platforms**, particularly older Android.
  Test on a real device; see `cross-device-testing`.

### Checklist

- [ ] Two families; a third justified in writing
- [ ] Display face tested at its real display size, not in a specimen
- [ ] Fluid scale uses `rem + vw` inside `clamp()`, never `vw` alone
- [ ] Tracking tightened at display sizes, loosened at caption sizes
- [ ] `text-wrap: balance` on headings, `pretty` on body
- [ ] Devanagari stack added with `unicode-range` split, if in scope
- [ ] `lang` attribute set on all non-English blocks
- [ ] Devanagari line height increased; rendering checked by a native reader
- [ ] Fonts self-hosted, subset, `font-display: swap`
- [ ] `crossorigin` present on every font preload
- [ ] Total font payload under 150 kB
- [ ] Body text ≥ 16 px; measure 60–75 characters

## References

- **Utopia — fluid type scale calculator**, the source of the `clamp()` two-pole
  method <https://utopia.fyi/type/calculator/>
- **MDN — `clamp()`, `text-wrap`, `font-optical-sizing`, `unicode-range`,
  `size-adjust`**
  <https://developer.mozilla.org/en-US/docs/Web/CSS/text-wrap>
- **WCAG 2.2 — SC 1.4.4 Resize Text, SC 3.1.2 Language of Parts**
  <https://www.w3.org/TR/WCAG22/#language-of-parts>
- **web.dev — "Best practices for fonts"**, on `font-display`, preload, and the
  `crossorigin` double-download trap <https://web.dev/articles/font-best-practices>
- **Fontsource — self-hosting open-licence fonts** <https://fontsource.org/>
- **Modern Font Stacks — zero-byte system stacks**
  <https://modernfontstacks.com/>
- **Tiro Typeworks — Tiro Devanagari <the local language>**, on its design for <the local language> and its
  OFL release <https://www.tiro.com/fonts/tiro-devanagari-marathi>
- **Butterick's *Practical Typography*** — measure, leading, restraint
  <https://practicaltypography.com/>

**Not sourced — written for this framework:** the display/body pairing table, the
optical-correction table, the 150 kB font budget, the specific Devanagari
line-height adjustments, and the detection commands.
