---
name: color-mood
version: 1.0.0
description: |
  Build a palette that carries emotion without fighting the photographs, and that
  passes contrast at every use. Use when defining or auditing a colour system for
  a photo-led site, when a design "looks cheap" in colour terms, or when a dark
  theme is requested.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## Colour and Mood

On a photo-led portfolio the palette has one primary duty: **do not compete with
the photographs.** Event photography is already saturated — flowers, lighting,
clothing, fireworks. A brand palette that is equally loud produces visual noise
and the images stop reading.

The counter-intuitive consequence: **premium photo sites are usually
near-monochrome with a single accent.**

> **Before running anything:** load `emotional-brief`. Colour temperature is
> derived from the event register, not chosen freely.

### Method

1. **Sample from the photographs.** Pull the dominant neutrals out of twenty real
   client images; that is the ground truth the palette must live beside.
2. **Build a neutral ramp first** — 10–12 steps. This is 90% of the interface.
3. **Add exactly one accent**, used for interactive elements only.
4. **Check every foreground/background pair** against WCAG before shipping.
5. **Derive the dark theme by role**, never by inverting the ramp.

### Structure: a neutral ramp and one accent

```css
:root {
  /* Neutral ramp — warm-shifted, sampled from the client's own photography.
     Warm neutrals sit beside skin tones better than pure greys. */
  --n-0:   #ffffff;
  --n-50:  #faf8f5;
  --n-100: #f2eee8;
  --n-200: #e4ded5;
  --n-300: #cfc6b9;
  --n-400: #a89d8e;
  --n-500: #7d7365;
  --n-600: #5c5349;
  --n-700: #423b33;
  --n-800: #2a2520;
  --n-900: #1a1714;
  --n-950: #0f0d0b;

  /* Exactly one accent. Interactive only — never decorative. */
  --accent:       #9a3b2f;
  --accent-hover: #7e2f25;

  /* Semantic roles. Components reference these, never the ramp directly. */
  --surface:        var(--n-50);
  --surface-raised: var(--n-0);
  --text:           var(--n-900);
  --text-muted:     var(--n-600);
  --border:         var(--n-200);
  --focus-ring:     var(--accent);
}
```

**Components must use the semantic roles, never the ramp.** A component
referencing `--n-700` cannot be re-themed; one referencing `--text-muted` can.
This is the single rule that makes a dark theme cheap instead of a rewrite.

### Why warm neutrals

Pure grey (`#808080`) beside warm skin tones reads as slightly blue and makes
photographs look colder than they are. Shifting the neutral ramp a few degrees
warm makes the whole page feel of a piece with the images. Cool neutrals suit
corporate/architectural work — which is exactly the register split in
`emotional-brief`.

### Temperature by register

| Register | Neutral shift | Accent character | Ground |
|---|---|---|---|
| Reverence | Warm, low chroma | Deep, desaturated (terracotta, oxblood) | Near-white or near-black |
| Exuberance | Warm, higher chroma | Saturated, high-energy | Light |
| Competence | Cool or true neutral | Restrained, single hue | Light |
| Delight | Warm | Bright, one only | Light |
| Gravitas | Cool, very low chroma | Almost none — use weight instead of colour | Dark |

### Contrast — the part that is not negotiable

WCAG 2.2 minimums: **4.5:1** for normal text, **3:1** for large text (≥24 px, or
≥18.66 px bold) and for UI component boundaries and focus indicators.

The three failures that recur on premium portfolio sites:

1. **Muted text.** `--text-muted` is the most common failure. Check it on both
   `--surface` and `--surface-raised`.
2. **Text over photographs.** A hero caption passes over the dark part of the
   image and fails over the bright part — and the image changes per breakpoint.
   **Never place text directly on an unmodified photograph.** Use a scrim:

```css
.hero-caption-bg {
  background-image: linear-gradient(
    to top,
    oklch(0% 0 0 / 0.75) 0%,
    oklch(0% 0 0 / 0.45) 40%,
    transparent 100%
  );
}
```

   Then verify contrast against the *darkest guaranteed* scrim value, not against
   the image.

3. **Focus indicators.** A 1 px accent outline on a mid-tone background usually
   fails 3:1. Use a two-tone ring that works on any ground:

```css
:focus-visible {
  outline: 2px solid var(--n-0);
  box-shadow: 0 0 0 4px var(--n-900);
  outline-offset: 2px;
}
```

**Never remove focus outlines.** WCAG 2.2 added SC 2.4.11 Focus Not Obscured and
SC 2.4.13 Focus Appearance; a portfolio with `outline: none` fails at the first
audit and is unusable by keyboard.

### Colour is never the only signal

WCAG SC 1.4.1. Every state carried by colour needs a second channel — icon,
text, weight, or underline. Links inside body copy should be underlined, not
merely coloured.

### Dark theme by role

```css
@media (prefers-color-scheme: dark) {
  :root:not([data-theme="light"]) {
    --surface:        var(--n-950);
    --surface-raised: var(--n-900);
    --text:           var(--n-100);
    --text-muted:     var(--n-400);
    --border:         var(--n-800);
    --accent:         #d4685a;   /* lightened — the light accent fails on dark */
  }
}
:root[data-theme="dark"] { /* same overrides, so the toggle wins */ }
```

**The accent must be re-picked for dark, not reused.** A hue that passes 4.5:1 on
white almost never passes on near-black.

**Dark is often the right default for an event portfolio** — it makes photographs
glow and suits evening events. But dark themes need *lower* image brightness and
more generous leading, and long body copy is harder to read on dark. Use dark for
galleries, light for reading pages, if the client will accept the split.

### Detection

```bash
# Components reaching past the semantic layer into the raw ramp
grep -rnE 'var\(--n-[0-9]+\)' src/components/ --include=*.astro --include=*.css

# Removed focus outlines
grep -rnE 'outline:\s*(none|0)' src/ --include=*.css

# Raw hex in components instead of tokens
grep -rnE '#[0-9a-fA-F]{3,8}\b' src/components/ --include=*.astro --include=*.css

# Text placed on an image with no scrim
grep -rn 'background-image' src/ --include=*.css | grep -i 'hero\|banner'
```

Automate the contrast check rather than eyeballing it — `axe-core` (MPL-2.0)
via Playwright catches every pair actually rendered. See `accessibility`.

### Caveats

- **Sampling from photographs can produce an unusable palette** if the archive is
  inconsistently graded. Grade first — see `art-direction`.
- **`oklch()` gives perceptually even ramps** and is well supported, but provide
  an `rgb`/hex fallback if the client's audience includes very old Android
  WebViews; check the real analytics before deciding.
- **Cultural colour meaning matters here.** In Indian wedding contexts red,
  saffron, and gold carry specific significance, and white has funerary
  associations in some communities. Do not apply a Western palette convention
  without asking the client.
- **Contrast tools measure text, not photographs.** A "beautiful low-contrast
  look" is an accessibility failure regardless of how it photographs.

### Checklist

- [ ] Neutral ramp sampled from the client's real photography
- [ ] Ramp warm- or cool-shifted per register, not pure grey
- [ ] Exactly one accent; interactive use only
- [ ] Semantic role layer defined; components never reference the ramp
- [ ] Every text/background pair checked at 4.5:1 (3:1 for large)
- [ ] Muted text verified on every surface it appears on
- [ ] No text on unmodified photographs; scrims verified at their darkest value
- [ ] Focus indicator visible on every ground, ≥3:1, never removed
- [ ] No state signalled by colour alone; body links underlined
- [ ] Dark theme derived by role, with a re-picked accent
- [ ] Contrast verified automatically, not by eye
- [ ] Cultural colour associations discussed with the client

## References

- **WCAG 2.2 — SC 1.4.1 Use of Color, SC 1.4.3 Contrast (Minimum), SC 1.4.11
  Non-text Contrast, SC 2.4.11 Focus Not Obscured, SC 2.4.13 Focus Appearance**
  <https://www.w3.org/TR/WCAG22/#contrast-minimum>
- **MDN — `oklch()` and CSS colour spaces**
  <https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/oklch>
- **MDN — `prefers-color-scheme`**
  <https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-color-scheme>
- **Open Props — token-layer structure for colour, easing, and shadow**
  <https://open-props.style/>
- **axe-core — colour-contrast rule**, the automated check referenced above
  <https://github.com/dequelabs/axe-core>

**Not sourced — written for this framework:** the near-monochrome-plus-one-accent
position, the warm-neutral rationale, the register temperature table, the
scrim-darkest-value verification rule, the dark-for-galleries/light-for-reading
split, and the detection commands.
