---
name: seo-foundations
version: 1.0.0
description: |
  Make a portfolio findable — crawlability, titles and descriptions, headings,
  internal linking, and the content depth a photo-heavy site tends to lack. Use
  when building pages, before launch, or when the site is not appearing in search.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## SEO Foundations

Portfolio sites have a structural SEO weakness: **they are mostly pictures.**
Search engines index text, and a beautiful case study with forty words of copy
gives them almost nothing to work with.

The fix is not keyword stuffing. It is that **the case-study structure this
framework requires — a brief, a problem, decisions, a facts table — happens to be
exactly the substantive text a search engine needs.** Good content and good SEO
converge here.

> **Before running anything:** confirm `site` is set in the Astro config.
> Without it, canonical URLs, the sitemap, and Open Graph URLs are all wrong —
> see `static-deploy`.

### Method

1. **Make sure it can be crawled** — the cheapest wins are here.
2. **Write unique titles and descriptions** for every page.
3. **Get the heading structure right.**
4. **Add the text the images cannot provide.**
5. **Link internally with meaningful anchors.**
6. **Verify in Search Console**, not by guessing.

### Crawlability

A static Astro site is nearly ideal by default — real HTML, real links, fast.
The ways it still goes wrong:

| Problem | Fix |
|---|---|
| Content only inside a `client:only` island | Server-render it → `astro-islands` |
| Navigation via JS handlers, not `<a href>` | Use real links |
| Staging site indexed | `noindex` on staging; check before launch |
| `robots.txt` blocking assets | Allow CSS and images — Google renders pages |
| Inconsistent trailing slashes | Pick one, redirect the other → `static-deploy` |
| Missing canonical | One canonical per page, absolute URL |

```astro
---
// In the base layout
const canonical = new URL(Astro.url.pathname, Astro.site);
---
<link rel="canonical" href={canonical} />
```

### Titles and descriptions

| Element | Length | Rules |
|---|---|---|
| `<title>` | 50–60 characters | Unique per page. Most distinctive words first |
| Meta description | 150–160 characters | Not a ranking factor, but it is the click decision |
| `<h1>` | One per page | May differ from the title |

```
Home:       Eventina Organisers — Wedding & Event Planners in Latur
Work index: Our Work — 12 Weddings and Events in Latur | Eventina
Case study: The Sharma Wedding, Latur — 310 Guests, One Venue Change
Service:    Wedding Planning in Latur | Eventina Organisers
```

**Put the distinguishing words first.** Titles truncate, and "Eventina
Organisers | " repeated at the start of every title wastes the visible portion.

```bash
# Duplicate titles across the built site — a common and costly fault
grep -rhoP '(?<=<title>).*?(?=</title>)' dist/**/*.html | sort | uniq -d
```

### Headings

- **One `<h1>`**, describing the page.
- **Never skip levels.** `h2` → `h4` is a structural error and an accessibility
  one → `accessibility`.
- **Headings describe sections**, they are not styling. A large-but-not-heading
  line of text is a `<p>` with a class.

### The content-depth problem

A case study with nine photographs and thirty words will not rank. The
`case-study-structure` sections supply the text:

| Section | Words | SEO value |
|---|---|---|
| Brief | 40–70 | Event type, location, client type |
| Problem | 40–80 | Long-tail phrases people actually search |
| What we did | 80–150 | Service terms in natural context |
| Facts table | — | Entities: venue, guest count, date |
| Alt text | ~15 each | Image search, and accessibility |
| Testimonial | ≤50 | Natural language, third-party voice |

That is 250–400 words of genuinely useful text per case study — enough, and
honestly earned.

**Alt text is real SEO value on a portfolio**, because image search is a genuine
discovery path for event work. Write it for humans and it works for both →
`accessibility`.

### Internal linking

- **Descriptive anchors.** "The Sharma wedding" not "read more" or "click here".
- **Link services to the case studies that demonstrate them**, both directions.
  This is the highest-value internal linking on a portfolio and it is almost
  always missing.
- **Every page reachable within three clicks** of the home page.
- **No orphan pages** — a case study reachable only from the sitemap will not
  rank.

```bash
grep -rn '>read more<\|>click here<\|>learn more<' src/ --include=*.astro
# Orphan check: slugs in content that no page links to
for f in src/content/events/*.md; do
  slug=$(basename "$f" .md)
  grep -rq "$slug" src/pages/ src/components/ || echo "orphan: $slug"
done
```

### URLs

```
/work/sharma-wedding-2026/          ✓ readable, stable, dated
/services/wedding-planning/         ✓
/work/?id=47                        ✗ parameters
/work/THE-SHARMA-WEDDING/           ✗ capitals
/w/sw26/                            ✗ unreadable
```

**Never change a URL after launch** without a 301. Changing case-study slugs is
a common cause of losing whatever ranking has accumulated.

### Detection

```bash
npm run build
grep -rL '<title>' dist --include=*.html                         # missing title
grep -rL 'meta name="description"' dist --include=*.html
grep -rL 'rel="canonical"' dist --include=*.html
grep -rc '<h1' dist/index.html                                    # exactly 1
grep -rn 'noindex' dist --include=*.html                          # not on production pages
test -f dist/sitemap-index.xml && echo "✓ sitemap"
grep -rhoP '(?<=<title>).*?(?=</title>)' dist/**/*.html | awk 'length > 60'
```

### Caveats

- **SEO for a local service business is mostly local SEO.** The Google Business
  Profile will drive more enquiries than on-page work → `local-discovery`.
- **A new domain takes months.** Set the expectation, or the client will conclude
  the site failed after three weeks.
- **Do not write for search engines.** Copy stuffed with "best wedding planner in
  Latur" damages the premium positioning that is the point of the site →
  `brand-narrative`.
- **Ranking is not the goal; enquiries are.** Ten visitors who call beat a
  thousand who bounce → `enquiry-conversion`.
- **This is a small site.** Technical SEO beyond the basics has little to offer;
  spend the effort on content and on Google Business Profile.

### Checklist

- [ ] `site` set; canonicals absolute and correct
- [ ] Real HTML content, not client-only rendered
- [ ] Navigation uses real `<a href>` links
- [ ] Trailing-slash policy consistent
- [ ] Unique `<title>` (50–60 chars) and description (150–160) per page
- [ ] Distinguishing words first in titles; no duplicates
- [ ] One `<h1>` per page; no skipped levels
- [ ] 250+ words of real text per case study
- [ ] Alt text written on every image
- [ ] Services ↔ case studies cross-linked
- [ ] Descriptive anchor text; no "read more"
- [ ] No orphan pages
- [ ] Readable, lowercase, stable URLs
- [ ] `robots.txt` allows CSS and images; sitemap submitted
- [ ] Staging not indexed
- [ ] Search Console verified

## References

- **Google Search Central — SEO Starter Guide**
  <https://developers.google.com/search/docs/fundamentals/seo-starter-guide>
- **Google Search Central — "Creating helpful, reliable, people-first content"**
  <https://developers.google.com/search/docs/fundamentals/creating-helpful-content>
- **Google Search Central — Titles and snippets in search results**
  <https://developers.google.com/search/docs/appearance/title-link>
- **Google Search Central — Canonical URLs and duplicate content**
  <https://developers.google.com/search/docs/crawling-indexing/consolidate-duplicate-urls>
- **Google Search Central — Google Images best practices**, on alt text as an
  image-search signal
  <https://developers.google.com/search/docs/appearance/google-images>
- **Astro docs — `@astrojs/sitemap`**
  <https://docs.astro.build/en/guides/integrations-guide/sitemap/>

**Not sourced — written for this framework:** the content-depth table mapping
case-study sections to SEO value, the services↔case-studies linking
recommendation, the title-ordering rule, the orphan-check script, and the other
detection commands.
