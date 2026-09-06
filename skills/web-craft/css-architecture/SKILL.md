---
name: css-architecture
version: 1.0.0
description: |
  Structure CSS for a portfolio so it stays predictable as it grows — token
  layer, cascade layers, scoping, and where styles live relative to components.
  Use when setting up styling, when specificity fights start, or when choosing
  between plain CSS, a utility framework, and a component library.
allowed-tools:
  - Read
  - Write
  - Edit
  - Grep
  - Glob
  - Bash
---

## CSS Architecture

A portfolio site has little CSS by volume and a lot of *bespoke* CSS — every case
study layout is slightly different. That combination punishes a heavy system and
rewards a small, strict one.

The framework's position: **a token layer plus cascade layers plus component-
scoped styles.** No methodology acronym required, and it survives adopting a
utility framework later.

> **Stack neutrality:** what matters is (1) a single token source, (2) an explicit
> cascade order, and (3) styles scoped to a component by default. The examples use
> Astro's scoped `<style>`; the same three rules apply with Tailwind, CSS modules,
> or a component library. Where the project adopts one of those, keep the rules
> and change the mechanism.

### Method

1. **Define the token layer** — one file, no component styles in it.
2. **Declare cascade layers in order**, once, before anything else.
3. **Put component styles in the component.**
4. **Reserve global CSS** for reset, tokens, and element defaults.
5. **Check specificity and dead CSS** before shipping.

### Layers, declared once

```css
/* src/styles/global.css — the first stylesheet loaded */
@layer reset, tokens, base, layout, components, utilities;
```

Later layers win regardless of specificity. This removes the whole class of
"why is this being overridden" problems, and it means a utility class beats a
component rule **without `!important`**.

| Layer | Holds |
|---|---|
| `reset` | Normalisation |
| `tokens` | Custom properties only |
| `base` | Element defaults — `h1`, `p`, `a`, `img` |
| `layout` | The page grid, containers — see `editorial-layout` |
| `components` | Component styles (Astro scopes these automatically) |
| `utilities` | Single-purpose overrides, deliberately last |

**Unlayered styles beat all layered styles.** So third-party CSS you cannot
control will win unless you wrap it: `@import "vendor.css" layer(components);`

### The token layer

One file. Two tiers: **primitives** (raw values) and **semantic roles** (what
components reference).

```css
@layer tokens {
  :root {
    /* Primitives — never referenced by components */
    --n-0: #fff;  --n-100: #f2eee8;  --n-600: #5c5349;  --n-900: #1a1714;
    --space-s: clamp(1rem, 0.91rem + 0.43vw, 1.25rem);
    --step-0:  clamp(1rem, 0.95rem + 0.22vw, 1.13rem);
    --ease-out: cubic-bezier(.22, 1, .36, 1);

    /* Semantic roles — the only thing components use */
    --surface:    var(--n-100);
    --text:       var(--n-900);
    --text-muted: var(--n-600);
    --border:     var(--n-100);
    --radius:     0.25rem;
  }
}
```

**Components reference roles, never primitives.** A component using `--n-600`
cannot be re-themed; one using `--text-muted` can. This is the rule that makes a
dark theme a ten-line change instead of a rewrite. See `color-mood`.

**Custom properties, not preprocessor variables** — they are inspectable in
devtools, overridable per component, and changeable at runtime.

### Component styles

```astro
<article class="card">
  <h3>{title}</h3>
</article>

<style>
  /* Astro scopes this automatically — no naming convention needed */
  .card {
    background: var(--surface);
    padding: var(--space-s);
    border-radius: var(--radius);
  }
  h3 { font-size: var(--step-1); }
</style>
```

Astro adds a data attribute to scope these rules, so `.card` in one component
cannot collide with `.card` in another. **That removes the reason BEM exists** —
do not add `.card__title--featured` naming on top of a scoping mechanism that
already works.

To style a child that Astro cannot see at build time, use `:global()` narrowly:

```css
.prose :global(p)   { margin-block: var(--space-s); }
.prose :global(img) { border-radius: var(--radius); }
```

Keep `:global()` inside a scoped selector. A bare `:global(p)` is a global style
hiding in a component file.

### Islands

Scoped styles do not cross into framework components. Options, in order:

1. **Style from the parent** with `:global()` on a wrapper class.
2. **Pass a class as a prop** and let the parent's scoped CSS reach it.
3. **CSS modules inside the island** for anything complex.

Do not reach for a CSS-in-JS runtime; it costs JavaScript on a site whose main
selling point is not shipping any. See `astro-islands`.

### If a utility framework is adopted

The project may add Tailwind later. The three rules survive:

- **Tokens become the framework's theme config** — still one source.
- **Utilities go in the `utilities` layer**, so they win over components by layer
  rather than by specificity.
- **Component-specific layout stays in the component**, not in a soup of
  thirty utility classes on one element.

Nothing above needs deleting to make that transition.

### Detection

```bash
grep -rn 'var(--n-[0-9]\|var(--step-[0-9]' src/components/ src/islands/  # primitives leaking
grep -rn '!important' src/ --include=*.css --include=*.astro             # should be ~zero
grep -rn ':global(' src/ --include=*.astro | grep -v '\.\w.*:global'     # bare globals
grep -rnE '#[0-9a-fA-F]{3,8}\b' src/components/ --include=*.astro        # hard-coded colour
grep -rnE '(margin|padding|gap):\s*[0-9.]+(px|rem)' src/components/ | grep -v 'var(--'
grep -c '@layer' src/styles/global.css
npm run build && du -sh dist/_astro/*.css
```

**Budget: under 40 kB of CSS across the site**, uncompressed. A portfolio that
exceeds that usually has duplicated layout rules.

### Caveats

- **Cascade layers change override behaviour.** Introducing them into an existing
  codebase can flip which rule wins. Add them at the start of a project, or
  migrate one layer at a time.
- **Scoped styles are per-component, not per-instance.** Two instances share one
  rule; per-instance variation goes through a custom property set inline.
- **`:global()` is a loaded gun.** Every use is a potential collision — grep for
  them in review.
- **Do not build a design system for one site.** A token layer and a handful of
  components is right; a component library with variants and documentation is
  over-engineering for a nine-page portfolio.

### Checklist

- [ ] `@layer` order declared once, before any other stylesheet
- [ ] Third-party CSS imported into a layer
- [ ] Tokens in one file, split into primitives and semantic roles
- [ ] Components reference only semantic roles
- [ ] Custom properties, not preprocessor variables
- [ ] Component styles live in the component, scoped
- [ ] No BEM-style naming layered on top of scoping
- [ ] `:global()` always nested inside a scoped selector
- [ ] Island styling handled without a CSS-in-JS runtime
- [ ] Zero `!important`
- [ ] No hard-coded colours or spacing in components
- [ ] Total CSS under 40 kB

## References

- **MDN — `@layer` and the cascade**, on layer order beating specificity
  <https://developer.mozilla.org/en-US/docs/Web/CSS/@layer>
- **MDN — CSS custom properties**
  <https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties>
- **Astro docs — Styling and scoped CSS**, including `:global()` and `is:global`
  <https://docs.astro.build/en/guides/styling/>
- **Open Props** (MIT) — a worked example of a primitive/semantic token split
  <https://open-props.style/>
- **Utopia** — the fluid `clamp()` values in the token examples
  <https://utopia.fyi/>
- **`color-mood`, `editorial-layout`, `editorial-typography`** (this framework) —
  the systems these tokens serve

**Not sourced — written for this framework:** the six-layer order, the
primitive/semantic rule as stated, the position against BEM under scoping, the
40 kB CSS budget, the utility-framework migration path, and the detection
commands.
