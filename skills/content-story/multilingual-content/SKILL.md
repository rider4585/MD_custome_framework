---
name: multilingual-content
version: 1.0.0
description: |
  Ship a site in <the local language> alongside English without the localisation being
  an afterthought — language markup, register, routing, fonts, and structured
  data. Use when a site targets a regional Indian market, when adding a second
  language, or when deciding whether to translate at all.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## Multilingual Content

For a business in a multilingual market, the language question is not "should we translate the
English site". Many customers read <the local language> comfortably and English adequately,
and **the language a premium service uses is itself a positioning signal** —
English can read as aspirational or as distant, depending on the customer.

**Decide the strategy before building anything.** Retrofitting a second language
into a single-language Astro site is expensive; the routing and content model
are affected.

> **Before running anything:** get the client's answer on strategy. This is a
> business decision, not a technical one. Do not default to English-only because
> it is easier.

### Method

1. **Choose a strategy** from the table below, with the client.
2. **Set up routing and `lang` markup** before writing content.
3. **Decide register** — formal vs familiar — explicitly.
4. **Confirm the font stack covers Devanagari** with correct metrics.
5. **Have a native speaker write, not translate**, the marketing copy.

### Strategies

| Strategy | When | Cost |
|---|---|---|
| **English only** | Customers are comfortable in English; budget is tight | Lowest. Risks reading as distant to some of the market |
| **English site, <the local language> accents** | Use <the local language> for emotive words the audience owns — *लग्न*, *संगीत*, *हळद* — inside English copy | Low, and often the best value. Signals local fluency without a full second site |
| **Full bilingual** | <the local language> is a primary reading language for a large share of enquiries | Highest. Doubles content, review, and CMS work — *forever*, not once |
| **<the local language>-first, English secondary** | The premium local market reads <the local language> and competitors are all in English | Differentiating, rarely chosen |

**Recommend "English site, <the local language> accents" as the default** unless the client
has evidence of <the local language>-preferring enquiries. A half-maintained second language
— stale <the local language> pages behind a fresh English site — is worse than one language
done well. That is the failure mode to avoid.

### Language markup

Non-negotiable regardless of strategy, and cheap:

```html
<html lang="en">
  ...
  <p>The <span lang="mr">हळद</span> was held on the terrace.</p>
```

`lang` on the root, and `lang` on **every** foreign-language run. This is WCAG
SC 3.1.1 and 3.1.2. It makes screen readers switch pronunciation voice — without
it, Devanagari read by an English voice is unintelligible.

### Routing and `hreflang` (full bilingual only)

```
src/pages/
  index.astro              →  /            (en)
  work/[slug].astro        →  /work/...
  mr/
    index.astro            →  /mr/
    work/[slug].astro      →  /mr/work/...
```

```html
<link rel="alternate" hreflang="en-IN" href="https://studio.example/work/x/">
<link rel="alternate" hreflang="mr-IN" href="https://studio.example/mr/work/x/">
<link rel="alternate" hreflang="x-default" href="https://studio.example/work/x/">
```

- **Reciprocal or ignored.** Each page must point at the other *and* at itself.
- **`x-default`** names the fallback for unmatched locales.
- **Never auto-redirect by IP or `Accept-Language`.** It traps users and blocks
  crawlers. Offer a persistent, visible switcher instead.
- **The switcher must go to the equivalent page**, not to the home page. Sending
  a reader from a case study to the <the local language> home page is the most common and most
  irritating bug in bilingual sites.

### Register

<the local language> and Hindi encode the client relationship grammatically. This is a
decision, not a default:

- **आपण / आपली** — formal, respectful. Right for addressing a customer.
- **तू / तुझी** — familiar. Wrong for a business addressing a client; reads as
  presumptuous.

Be consistent across the site, including form labels and error messages, which
are usually where an inconsistent register slips through.

### Typography

Devanagari needs different metrics from Latin — increased line height, and often
a size bump. Full treatment in `editorial-typography`; the essentials:

```css
:lang(mr), :lang(hi) {
  line-height: 1.7;                    /* vs 1.5 for Latin */
  font-family: 'Mukta', 'Noto Sans Devanagari', sans-serif;
}
```

Serve Devanagari via a `unicode-range`-scoped `@font-face` so English-only
readers never download it.

**Have a native reader check rendered output on a real Android device.**
Conjunct forms and the <the local language> eyelash *ḷa* render differently across platforms,
and no automated test catches it.

### Content model

Do not duplicate the collection. Add a locale field and pair translations:

```ts
schema: z.object({
  lang: z.enum(['en','mr']).default('en'),
  translationOf: z.string().optional(),   // slug of the English original
  // ...
})
```

This lets you generate `hreflang` automatically and detect untranslated pages at
build time — the check that prevents the stale-second-language failure.

### Structured data and metadata

- `inLanguage` on `Event` and `LocalBusiness` schema.
- Translate `<title>`, meta description, and Open Graph tags. An English OG
  description on a <the local language> page is a common oversight; see `social-sharing`.
- `og:locale` and `og:locale:alternate`.

### Detection

```bash
grep -rn '<html' src/ --include=*.astro | grep -v 'lang='          # missing root lang
grep -rnP '[\x{0900}-\x{097F}]' src/ --include=*.astro | grep -v 'lang='  # unmarked Devanagari
grep -rn 'hreflang' src/ | wc -l                                    # present at all
grep -rn 'Accept-Language\|geoip' src/                              # auto-redirect smell
```

### Caveats

- **Machine translation is not acceptable for marketing copy.** It reads as
  translated, which undercuts a premium positioning precisely where it matters.
  It is adequate for a first draft a native speaker then rewrites.
- **A second language doubles maintenance forever.** Make sure the client
  understands they are committing to updating both, and build the
  untranslated-page check so drift is visible.
- **the local market may read <the local language>, but the wedding market may include
  Hindi-preferring families.** Ask; do not assume from geography.
- **The CMS must support the second language too.** Sveltia CMS has first-class
  i18n; Decap's is weaker. See `git-cms`.

### Checklist

- [ ] Strategy chosen with the client, with reasoning recorded
- [ ] `lang` on `<html>` and on every foreign-language run
- [ ] Register (formal vs familiar) decided and applied consistently
- [ ] `hreflang` reciprocal, self-referencing, with `x-default` — if bilingual
- [ ] No automatic redirect by IP or `Accept-Language`
- [ ] Language switcher goes to the equivalent page
- [ ] Devanagari font scoped by `unicode-range`; line height increased
- [ ] Rendering checked by a native reader on a real Android device
- [ ] Content model pairs translations; untranslated pages fail the build
- [ ] Titles, meta descriptions, and OG tags translated
- [ ] `inLanguage` set in structured data
- [ ] Marketing copy written by a native speaker, not translated

## References

- **WCAG 2.2 — SC 3.1.1 Language of Page, SC 3.1.2 Language of Parts**
  <https://www.w3.org/TR/WCAG22/#language-of-parts>
- **Google Search Central — "Localized versions of your pages"**, on `hreflang`
  reciprocity, `x-default`, and why automatic redirection harms crawling
  <https://developers.google.com/search/docs/specialty/international/localized-versions>
- **MDN — the `lang` attribute and `:lang()` selector**
  <https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Global_attributes/lang>
- **MDN — `unicode-range` for script-scoped font loading**
  <https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face/unicode-range>
- **Google Fonts — Mukta, Noto Sans Devanagari, Tiro Devanagari <the local language>** (all
  SIL OFL 1.1) <https://fonts.google.com/?subset=devanagari>
- **Astro docs — internationalization routing**
  <https://docs.astro.build/en/guides/internationalization/>

**Not sourced — written for this framework:** the four-strategy table and the
recommendation of "English with <the local language> accents", the stale-second-language
failure-mode argument, the `translationOf` build-time check, and the detection
commands.
