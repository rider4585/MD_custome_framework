---
name: project-site-architecture
version: 1.0.0
description: |
  The page map, URL structure, and navigation for this photography studio's
  site, and the reasoning behind each. Use when adding a page, deciding where
  content belongs, or defining routes and redirects.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## Project: Site Architecture

A photographer's portfolio needs **fewer pages than anyone expects**. Every extra
page dilutes the work, adds maintenance, and gives a visitor another way to not
reach the enquiry form.

**Twelve pages plus case studies and venue pages.** That is the proposal.

> **Before running anything:** load `project-shoot-catalogue` — the service pages
> depend on services that are not yet confirmed — and resolve the
> **stills-vs-film question**, which changes the navigation itself.

### Page map

```
/                              Home
/work/                         Portfolio index — case studies, filterable
/work/<slug>/                  Case study                    (6–12 of these)
/films/                        Films index                   (only if film is a real product)
/films/<slug>/                 One film                      (optional; may live inside /work/)
/services/<slug>/              One service                   (2–4 of these)
/pricing/                      Packages, inclusions, travel rule
/process/                      Enquiry → day → delivery
/about/                        The photographer, the signature, the refusal
/venues/<slug>/                One venue, real knowledge     (grows over time)
/contact/                      Enquiry form + WhatsApp + phone
/clients/                      Entry point to gallery delivery
/licensing/                    Image licence terms
/privacy/                      Privacy notice, erasure contact
/thank-you/                    Post-enquiry                  (noindex)
/404                           Not found
```

**Differences from a general event-portfolio map, and why:**

| Page | Why it exists here |
|---|---|
| `/pricing/` | Couples search for price and a photographer who hides it burns enquiry capacity → `booking-and-packages` |
| `/process/` | A couple has never hired a photographer. Uncertainty, not price, stalls the enquiry |
| `/films/` | Film is a parallel product with a different buying decision → `film-showcase` |
| `/venues/<slug>/` | The highest-return SEO surface available to a wedding photographer → `vendor-network` |
| `/clients/` | A visible, branded doorway to the delivery system, which lives elsewhere → `client-gallery-delivery` |
| `/licensing/` | Required by the licensable-image markup, and it must be real → `image-rights-and-credit` |
| No `/services/` overview | With 2–4 services an overview page is a click that leads to another click |

**Pages deliberately not included:**

| Page | Why not |
|---|---|
| Blog | Nobody maintains it. An abandoned blog dated 2026 damages credibility. Venue pages carry the SEO instead |
| Separate gallery | Photographs without a story is what competitors have |
| Testimonials page | Quotes belong beside the work they describe → `testimonial-curation` |
| FAQ | Only if enquiries reveal genuinely repeated questions. Not up front |
| Vendor/partner logo wall | Converts nothing and reads as a link scheme → `vendor-network` |

### What each page must do

| Page | One job | Primary CTA |
|---|---|---|
| Home | Show the signature in ten seconds → `signature-style` | "Check your date" |
| Work index | Let a couple find work like theirs | Enter a case study |
| Case study | Prove they would have seen the moments that matter → `wedding-story-arc` | **Enquire — highest intent** |
| Films index | Sell the film product | Play a teaser, then enquire |
| Service | Sell one shoot type, with proof | Enquire about this |
| Pricing | Qualify. Let the wrong couple leave | Enquire, with the date |
| Process | Remove uncertainty | Enquire |
| About | Make the photographer a person, and state the refusal | Enquire |
| Venue | Be the most useful page about that venue on the web | Enquire |
| Contact | Remove every obstacle → `enquiry-conversion` | Form / WhatsApp |
| Clients | Get an existing client to their gallery in one click | The gallery link |
| Licensing | State real terms | — |
| Thank you | Confirm and set expectations | WhatsApp, for the impatient |

### URL rules

- **Lowercase, hyphenated, no parameters.**
- **Trailing slashes, consistently** — `trailingSlash: 'always'` → `static-deploy`.
- **Case-study slugs include the year**: `/work/priya-rohan-2026/`. Disambiguates
  and reads well.
- **Venue slugs are the venue's searched name**: `/venues/hotel-name-latur/`.
- **Service slugs use the terms couples search**: `/services/pre-wedding-shoot/`,
  not `/services/couple-portraiture/` → `project-shoot-catalogue`.
- **Never change a slug after launch** without a 301 → `seo-foundations`.
- **Delivery galleries are never under this domain's route tree** — separate
  subdomain, `noindex` → `client-gallery-delivery`.

### Navigation

**Five or six items maximum in the header.**

```
<Studio>     Work    Films    Pricing    About    Contact →
```

- **"Work" first.** It is the reason to be on the site.
- **"Films" only if film is a real product.** If it is not, the item is noise and
  the films live inside case studies → `project-shoot-catalogue`.
- **"Pricing" named that**, whatever heading the page carries. Couples search the
  word → `booking-and-packages`.
- **Contact visually distinct** — a button, not a link.
- **No dropdowns.** A persistent accessibility cost for no gain at this size →
  `accessibility`.
- **Mobile: a sticky bottom bar** with WhatsApp and Call → `enquiry-conversion`.
- **Footer** carries the full NAP block, social links, `/clients/`,
  `/licensing/`, and privacy → `local-discovery`.

`/clients/`, `/process/`, and `/venues/` live in the footer and in contextual
links, not the header. Existing clients will find `/clients/`; prospects should
not be offered it.

### The work index

The page that most needs a decision, because it is where the portfolio either
reads as curated or as a directory.

- **Filter by shoot type**, not by year → `project-shoot-catalogue`.
- **Filtering must work without JavaScript** — server-render all items, filter by
  hiding → `astro-islands`.
- **Show 9–12, not everything.** Selection is the premium signal, and it is the
  signature made visible → `signature-style`, `brand-narrative`.
- **Each card: one image, couple/event name, shoot type, venue.** No excerpt —
  the image does the work → `art-direction`.
- **No pagination** at this scale. Pagination on twelve items signals a directory.

### Depth

**Every page reachable within three clicks of home**, and every case study
reachable from at least two places — the work index, and the venue page for where
it was shot. That second path is why venue pages compound: they are internal
links from a page that ranks → `seo-foundations`, `vendor-network`.

### Redirects

If an existing site or links are being replaced:

```
# public/_redirects
/gallery        /work/                301
/portfolio      /work/                301
/video          /films/               301
/contact-us     /contact/             301
```

**Ask the client for their existing URLs before launch** — the Instagram bio
link, printed cards, and any directory listings may point at paths that must keep
working → `local-discovery`.

### Caveats

- **This map is a proposal**, not a decision. It assumes a service mix and a
  film product that are both unverified → `project-photographer-brand`.
- **The film question changes the navigation.** Do not build routes before it is
  answered.
- **Venue pages must not be pre-built for venues the studio has not shot.**
  The route exists; the pages arrive with the work → `vendor-network`.
- **Fewer pages is harder to sell than more pages.** Clients equate page count
  with value. Each page must earn its maintenance.
- **If Marathi is added, this doubles** → `multilingual-content`. Decide before
  building routes.

### Checklist

- [ ] Stills-vs-film question resolved before routes are built
- [ ] Page map agreed with the client, including what is excluded and why
- [ ] Each page has one stated job and one primary CTA
- [ ] URLs lowercase, hyphenated, trailing-slash consistent
- [ ] Case-study slugs include the year; venue slugs use searched names
- [ ] Six or fewer header items; no dropdowns
- [ ] `/clients/` and `/process/` in the footer, not the header
- [ ] Contact visually distinct in the header
- [ ] Sticky mobile contact bar present
- [ ] Footer carries NAP, social, clients, licensing, and privacy links
- [ ] Work index filters by shoot type and works without JavaScript
- [ ] Every case study linked from both the work index and a venue page
- [ ] No delivery gallery URL anywhere in this site's routes or sitemap
- [ ] `/licensing/` states real terms, matching the image metadata
- [ ] Existing URLs collected from the client and redirected
- [ ] `/thank-you/` noindexed
- [ ] Bilingual routing decided before routes are built

## References

- **`project-shoot-catalogue`, `case-study-structure`, `booking-and-packages`,
  `film-showcase`, `vendor-network`, `client-gallery-delivery`,
  `enquiry-conversion`, `seo-foundations`, `static-deploy`, `astro`**
  (this framework) — the decisions this map depends on and feeds
- **Nielsen Norman Group — flat vs. deep hierarchies**
  <https://www.nngroup.com/articles/flat-vs-deep-hierarchy/>
- **Google Search Central — site structure and crawlable links**
  <https://developers.google.com/search/docs/crawling-indexing/links-crawlable>
- **Astro docs — routing and `trailingSlash`**
  <https://docs.astro.build/en/guides/routing/>

**Not sourced — written for this framework:** the page map and its deliberate
exclusions, the pricing/process/venues/clients additions and their reasoning, the
one-job-per-page table, the header-vs-footer split, the work-index rules, and the
two-path depth requirement.
