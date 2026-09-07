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
| Home | `LocalBusiness` (or `EventPlanner` subtype if apt) | Knowledge panel support |
| Case study | `Event` + `ImageObject` | Limited — `Event` rich results target ticketed events |
| Services | `Service` | No |
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
  "@id": "https://eventina.in/#business",
  "name": "Eventina Organisers",
  "description": "Wedding and event planning in Latur, Maharashtra.",
  "url": "https://eventina.in/",
  "telephone": "+91XXXXXXXXXX",
  "email": "hello@eventina.in",
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
  "image": "https://eventina.in/og-default.jpg",
  "sameAs": [
    "https://www.instagram.com/eventina.organisers/",
    "https://www.facebook.com/Eventina.in/"
  ]
};
---
<script type="application/ld+json" set:html={JSON.stringify(business)} />
```

**Every value must be verified before publishing.** Address, phone, and founding
date in `project-eventina-brand` are currently `[to verify]` from a directory
listing — publishing an unverified address in structured data is worse than
publishing none, because it can propagate.

**`sameAs` matters.** It links the site to the Instagram and Facebook profiles,
helping search engines resolve them as one entity.

**Astro's `set:html` on a JSON-LD script** avoids the HTML-escaping that breaks
JSON when interpolated normally.

### Event, per case study

```astro
---
const { event } = Astro.props;
const schema = {
  "@context": "https://schema.org",
  "@type": "Event",
  "name": event.data.title,
  "startDate": event.data.date.toISOString().split('T')[0],
  "eventStatus": "https://schema.org/EventScheduled",
  "eventAttendanceMode": "https://schema.org/OfflineEventAttendanceMode",
  "location": {
    "@type": "Place",
    "name": event.data.facts.venue ?? "Latur",
    "address": { "@type": "PostalAddress", "addressLocality": "Latur",
                 "addressRegion": "Maharashtra", "addressCountry": "IN" }
  },
  "image": event.data.gallery.slice(0, 3).map(g => new URL(g.src.src, Astro.site).href),
  "description": event.data.brief,
  "organizer": { "@id": "https://eventina.in/#business" }
};
---
<script type="application/ld+json" set:html={JSON.stringify(schema)} />
```

**`@id` references** link the event to the business without repeating the whole
object — this is how you build a connected graph rather than isolated blobs.

**Generate this from the collection entry**, never hand-write it per page. Hand-
written JSON-LD drifts from the page within weeks → `content-collections`.

### Breadcrumbs

The most reliable visible rich result of the set.

```js
{
  "@context": "https://schema.org",
  "@type": "BreadcrumbList",
  "itemListElement": [
    { "@type": "ListItem", "position": 1, "name": "Home", "item": "https://eventina.in/" },
    { "@type": "ListItem", "position": 2, "name": "Work", "item": "https://eventina.in/work/" },
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
- **`Event` markup for past events** is legitimate as description, but expect no
  rich result — Google's event features target upcoming, ticketed events.
- **`FAQPage` rich results were heavily restricted in 2023.** Add it if the FAQ
  is genuinely useful, not for the snippet.
- **Duplicate `@id` values** across pages create a confused graph. Keep them
  unique and stable.

### Checklist

- [ ] JSON-LD used throughout, not microdata
- [ ] `LocalBusiness` on the home page with verified NAP
- [ ] Every value verified — nothing `[to verify]` published
- [ ] `sameAs` links to Instagram and Facebook
- [ ] `Event` generated from the collection, not hand-written
- [ ] `@id` references connect events to the business
- [ ] `BreadcrumbList` matching visible breadcrumbs
- [ ] No `AggregateRating` for third-party reviews
- [ ] Nothing marked up that is not visible on the page
- [ ] JSON validity checked in CI
- [ ] Schema Markup Validator and Rich Results Test both run
- [ ] Client told which types produce a visible result and which do not
- [ ] Search Console structured-data reports monitored after launch

## References

- **Schema.org** — `LocalBusiness`, `Event`, `Place`, `ImageObject`,
  `BreadcrumbList`, `Service` <https://schema.org/LocalBusiness>
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
honest rich-result expectations, the generate-from-collection rule, the
`@id`-graph approach, and the validation script.
