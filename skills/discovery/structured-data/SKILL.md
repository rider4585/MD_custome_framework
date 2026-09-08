---
name: structured-data
version: 1.0.0
description: |
  Mark up a portfolio with schema.org so search engines understand the business,
  its events, and its work — LocalBusiness, Event, ImageObject, FAQ, breadcrumbs,
  and the review-markup rules. Use when adding structured data, or when rich
  results are not appearing.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## Structured Data

Structured data tells a search engine what a page *is* rather than making it
infer. For a local service business with dated, located, photographed work, the
vocabulary fits unusually well.

**Use JSON-LD.** Google recommends it, it lives in one block, and it does not
entangle markup with metadata.

> **Before running anything:** structured data must describe what is actually on
> the page. Marking up content that is not visible is a guidelines violation and
> can earn a manual action — this is the rule that most often gets sites into
> trouble.

### Method

1. **Map each page type to a schema type.**
2. **Emit JSON-LD from the content collection**, so it cannot drift from the page.
3. **Validate every template.**
4. **Confirm eligibility** — most types produce no visible rich result.
5. **Monitor Search Console** after launch.

### Page-to-type mapping

| Page | Type | Rich result? |
|---|---|---|
| Home | `LocalBusiness` → `ProfessionalService`. There is no photography subtype | Knowledge panel support |
| Case study | `ImageGallery` + licensable `ImageObject` | **Yes** — the licensable badge in Google Images |
| Film page | `VideoObject` | **Yes** — video rich results and the video tab |
| Services / pricing | `Service`, with `Offer` only if prices are published | No |
| Venue page | `Article` or plain — **never** `Place` implying ownership | No |
| All pages | `BreadcrumbList` | Yes — breadcrumbs in results |
| FAQ page | `FAQPage` | Restricted since 2023; mostly government and health sites now |
| Contact | `LocalBusiness` with `ContactPoint` | Supports the knowledge panel |

**Be honest with the client about which of these produce a visible result.**
Most do not. They still help search engines understand the business, which is
worth doing — but promising "rich snippets" from `Event` markup on a past wedding
would be overselling.

### LocalBusiness — the one that matters most

This is the highest-value markup for this business, because it reinforces what
the Google Business Profile says → `local-discovery`.

```astro
---
const business = {
  "@context": "https://schema.org",
  "@type": "LocalBusiness",
  "@id": "https://studio.example/#business",
  "name": "the studio",
  "description": "Wedding photography and films in Latur, Maharashtra.",
  "url": "https://studio.example/",
  "telephone": "+91XXXXXXXXXX",
  "email": "hello@studio.example",
  "address": {
    "@type": "PostalAddress",
    "streetAddress": "Kailash Plaza, Beside Manas Hotel, Barshi Road, Ganj Golai",
    "addressLocality": "Latur",
    "addressRegion": "Maharashtra",
    "postalCode": "413512",
    "addressCountry": "IN"
  },
  "geo": { "@type": "GeoCoordinates", "latitude": 0, "longitude": 0 },
  "areaServed": [
    { "@type": "City", "name": "Latur" },
    { "@type": "State", "name": "Maharashtra" }
  ],
  "foundingDate": "2016",
  "image": "https://studio.example/og-default.jpg",
  "sameAs": [
    "https://www.instagram.com/creative_weddings_films_latur/"
  ]
};
---
<script type="application/ld+json" set:html={JSON.stringify(business)} />
```

**Every value must be verified before publishing.** Address, phone, and founding
date in `project-photographer-brand` are currently `[to verify]` from a directory
listing — publishing an unverified address in structured data is worse than
publishing none, because it can propagate.

**`sameAs` matters.** It links the site to the Instagram and Facebook profiles,
helping search engines resolve them as one entity.

**Astro's `set:html` on a JSON-LD script** avoids the HTML-escaping that breaks
JSON when interpolated normally.

### Case study — ImageGallery with licensable images

For a photographer this replaces the `Event` markup an event-organiser site would
use. A past private wedding is not an `Event` in the sense Google's event
features mean — those target upcoming, public, ticketed events — and marking one
up produces nothing. **The valuable markup is on the images themselves.**

```astro
---
const { study } = Astro.props;
const schema = {
  "@context": "https://schema.org",
  "@type": "ImageGallery",
  "name": study.data.title,
  "description": study.data.brief,
  "datePublished": study.data.published.toISOString().split('T')[0],
  "author": { "@id": "https://studio.example/#business" },
  "about": study.data.venue ?? undefined,
  "associatedMedia": study.data.gallery.map((g) => ({
    "@type": "ImageObject",
    "contentUrl": new URL(g.src.src, Astro.site).href,
    "caption": g.alt,
    "creator": { "@id": "https://studio.example/#business" },
    "creditText": study.data.credits.photography,
    "copyrightNotice": `© ${study.data.year} ${study.data.credits.photography}`,
    "license": new URL('/licensing/', Astro.site).href,
    "acquireLicensePage": new URL('/contact/', Astro.site).href
  }))
};
---
<script type="application/ld+json" set:html={JSON.stringify(schema)} />
```

**`license` and `acquireLicensePage` are what earn the licensable badge** in
Google Images, and they are among the few structured-data features that reliably
produce a visible result for a photographer. Both are required; one alone does
nothing → `image-rights-and-credit`.

**The values must match the embedded IPTC metadata exactly.** Two sources of
truth for copyright is worse than one.

**`/licensing/` must be a real page stating real terms.** Pointing `license` at a
page that does not describe a licence is a guidelines violation.

### Films — VideoObject

Video is the other reliably visible rich result available here.

```js
{
  "@context": "https://schema.org",
  "@type": "VideoObject",
  "name": "Priya & Rohan — Wedding Film",
  "description": "A four-minute highlight film from a two-day wedding in Latur.",
  "thumbnailUrl": ["https://studio.example/films/priya-rohan-poster.jpg"],
  "uploadDate": "2026-03-14T00:00:00+05:30",
  "duration": "PT4M12S",                       // ISO 8601 — PT4M12S, not "4:12"
  "contentUrl": "https://studio.example/films/priya-rohan.mp4",
  "embedUrl": "https://player.vimeo.com/video/000000000",
  "creator": { "@id": "https://studio.example/#business" },
  "isFamilyFriendly": true
}
```

- **`thumbnailUrl` is required** and must be a real, crawlable image — the same
  poster frame the facade shows → `film-showcase`.
- **`duration` is ISO 8601.** `PT4M12S`. A human-readable string is silently
  ignored, which is the most common mistake in video markup.
- **`uploadDate` needs a timezone offset.**
- **Provide `contentUrl` or `embedUrl`, ideally both.** Facade-loading the player
  does not affect eligibility, because the markup is in the HTML either way.
- Add markup only for films actually on the page. A films index listing five
  films may carry five `VideoObject`s; a page with none may carry none.

### Breadcrumbs

The most reliable visible rich result of the set.

```js
{
  "@context": "https://schema.org",
  "@type": "BreadcrumbList",
  "itemListElement": [
    { "@type": "ListItem", "position": 1, "name": "Home", "item": "https://studio.example/" },
    { "@type": "ListItem", "position": 2, "name": "Work", "item": "https://studio.example/work/" },
    { "@type": "ListItem", "position": 3, "name": "The Sharma Wedding" }
  ]
}
```

The last item has no `item` — it is the current page. **Breadcrumb markup must
match visible breadcrumbs on the page.**

### Reviews — where sites get penalised

- **Do not mark up third-party reviews as your own `AggregateRating`.** Google's
  guidelines restrict self-serving review markup, and aggregating a Justdial
  rating into your own schema is exactly the pattern that draws a manual action
  → `testimonial-curation`.
- **Reviews you host and display** may be marked up, and only if visible.
- **The safe route for this business** is to let Google Business Profile carry
  the ratings, and link to the directory listings as evidence in visible content.

### Validation

```bash
# Extract and validate JSON-LD from the built output
npm run build
for f in dist/index.html dist/work/index.html dist/work/*/index.html; do
  echo "── $f"
  grep -oP '(?<=<script type="application/ld\+json">).*?(?=</script>)' "$f" \
    | jq -e . > /dev/null && echo "  ✓ valid JSON" || echo "  ✗ INVALID JSON"
done

grep -rn 'application/ld+json' src/ --include=*.astro | wc -l
grep -rn 'aggregateRating' src/ --include=*.astro     # review the guidelines first
```

Then run the official validators:

- **[Schema Markup Validator](https://validator.schema.org/)** — syntax and
  vocabulary
- **[Google Rich Results Test](https://search.google.com/test/rich-results)** —
  eligibility for a visible result

**Both.** The first says the markup is valid; the second says whether Google will
do anything with it.

### Caveats

- **Structured data is not a ranking factor in itself.** It affects
  understanding and eligibility for features, not position.
- **Invalid JSON fails silently.** A trailing comma means the whole block is
  ignored with no error anywhere. Validate in CI.
- **Do not mark up what is not visible.** The most common way to earn a manual
  action.
- **Do not mark up a past private wedding as an `Event`.** Google's event
  features target upcoming, public, ticketed events; the markup produces nothing
  and misdescribes the page.
- **The licensable badge is eligibility, not a guarantee** — like every
  structured-data feature.
- **Video markup will not rescue a film nobody watches.** It affects discovery in
  the video tab, not completion → `film-showcase`.
- **`FAQPage` rich results were heavily restricted in 2023.** Add it if the FAQ
  is genuinely useful, not for the snippet.
- **Duplicate `@id` values** across pages create a confused graph. Keep them
  unique and stable.

### Checklist

- [ ] JSON-LD used throughout, not microdata
- [ ] `LocalBusiness` on the home page with verified NAP
- [ ] Every value verified — nothing `[to verify]` published
- [ ] `sameAs` links to Instagram and Facebook
- [ ] `ImageGallery` + `ImageObject` generated from the collection, not hand-written
- [ ] `license` and `acquireLicensePage` present on every published image
- [ ] `/licensing/` exists and states real terms
- [ ] `creditText` / `copyrightNotice` match the embedded IPTC values exactly
- [ ] `VideoObject` on every film page, with a real `thumbnailUrl` and ISO 8601 `duration`
- [ ] No `Event` markup on past private weddings
- [ ] `@id` references connect events to the business
- [ ] `BreadcrumbList` matching visible breadcrumbs
- [ ] No `AggregateRating` for third-party reviews
- [ ] Nothing marked up that is not visible on the page
- [ ] JSON validity checked in CI
- [ ] Schema Markup Validator and Rich Results Test both run
- [ ] Client told which types produce a visible result and which do not
- [ ] Search Console structured-data reports monitored after launch

## References

- **Schema.org** — `LocalBusiness`, `ProfessionalService`, `ImageGallery`,
  `ImageObject`, `VideoObject`, `BreadcrumbList`, `Service`
  <https://schema.org/LocalBusiness>
- **Google Search Central — Image licence structured data**, the `license` +
  `acquireLicensePage` requirement for the licensable badge
  <https://developers.google.com/search/docs/appearance/structured-data/image-license-metadata>
- **Google Search Central — Video structured data**, `thumbnailUrl`,
  `uploadDate`, and the ISO 8601 `duration` requirement
  <https://developers.google.com/search/docs/appearance/structured-data/video>
- **Google Search Central — Introduction to structured data markup**
  <https://developers.google.com/search/docs/appearance/structured-data/intro-structured-data>
- **Google Search Central — Structured data general guidelines**, on marking up
  only visible content and the resulting manual actions
  <https://developers.google.com/search/docs/appearance/structured-data/sd-policies>
- **Google Search Central — Local business structured data**
  <https://developers.google.com/search/docs/appearance/structured-data/local-business>
- **Google Search Central — Review snippet guidelines**, on self-serving reviews
  <https://developers.google.com/search/docs/appearance/structured-data/review-snippet>
- **Schema Markup Validator** <https://validator.schema.org/> and
  **Rich Results Test** <https://search.google.com/test/rich-results>
- **Astro docs — `set:html` directive**
  <https://docs.astro.build/en/reference/directives-reference/#sethtml>

**Not sourced — written for this framework:** the page-to-type mapping with
honest rich-result expectations, the argument against `Event` markup on private
weddings, the generate-from-collection rule, the metadata-must-match rule, the
`@id`-graph approach, and the validation script.
