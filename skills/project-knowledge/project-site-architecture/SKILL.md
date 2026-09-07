---
name: project-site-architecture
version: 1.0.0
description: |
  The page map, URL structure, and navigation for the Eventina site, and the
  reasoning behind each. Use when adding a page, deciding where content belongs,
  or defining routes and redirects.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## Project: Site Architecture

A portfolio for a service business needs **fewer pages than anyone expects**.
Every additional page dilutes the work, adds maintenance, and gives a visitor
another way to not reach the contact form.

**Nine pages plus case studies.** That is the proposal.

> **Before running anything:** load `project-event-catalogue` — the service pages
> depend on services that are not yet confirmed.

### Page map

```
/                              Home
/work/                         Portfolio index — all case studies, filterable
/work/<slug>/                  Case study            (6–12 of these)
/services/                     What we do — overview
/services/<slug>/              One service           (3–6 of these)
/about/                        Who we are, the team
/contact/                      Enquiry form + WhatsApp + phone + address
/thank-you/                    Post-enquiry          (noindex)
/privacy/                      Privacy notice, erasure contact
/404                           Not found
```

**Pages deliberately not included:**

| Page | Why not |
|---|---|
| Blog | Nobody maintains it. An abandoned blog dated 2026 actively damages credibility |
| Pricing | A premium service sells through conversation → `brand-narrative` |
| Testimonials page | Quotes belong beside the work they describe → `testimonial-curation` |
| Gallery (separate from work) | Photographs without a story is what competitors have |
| FAQ | Only if enquiries reveal genuinely repeated questions. Not up front |

**Add a page only when a real need appears.** Every one of the above can be added
later; none can be un-abandoned once stale.

### What each page must do

| Page | One job | Primary CTA |
|---|---|---|
| Home | Prove capability in ten seconds | "Check your date" |
| Work index | Let a visitor find work like theirs | Enter a case study |
| Case study | Prove competence on one event → `case-study-structure` | **Enquire — highest intent** |
| Services overview | Say what is sold | Enter a service |
| Service | Sell one service, with proof | Enquire about this service |
| About | Make the company human and real | Enquire |
| Contact | Remove every obstacle → `enquiry-conversion` | The form / WhatsApp |
| Thank you | Confirm and set expectations | WhatsApp, for the impatient |
| Privacy | Meet the obligation → `media-consent` | — |

### URL rules

- **Lowercase, hyphenated, no parameters.**
- **Trailing slashes, consistently** — `trailingSlash: 'always'` →
  `static-deploy`.
- **Case-study slugs include the year**: `/work/sharma-wedding-2026/`. Disambiguates
  repeat clients and reads well.
- **Never change a slug after launch** without a 301 → `seo-foundations`.
- **Service slugs use the terms clients search**: `/services/wedding-planning/`,
  not `/services/full-service-event-management/`.

### Navigation

**Five items maximum in the header.**

```
Eventina        Work    Services    About    Contact →
```

- **"Work" first.** It is the reason to be on the site.
- **Contact visually distinct** — a button, not a link.
- **No dropdowns.** With three services a dropdown adds an interaction for
  nothing, and dropdowns are a persistent accessibility cost →
  `accessibility`.
- **Mobile: a sticky bottom bar** with WhatsApp and Call, plus a standard menu
  → `enquiry-conversion`.
- **Footer carries the full NAP block**, social links, and the privacy link →
  `local-discovery`.

### The work index

The page that most needs a decision, because it is where the portfolio either
reads as curated or as a directory.

- **Filter by event type**, not by year. Visitors are looking for work like
  theirs → `project-event-catalogue`.
- **Filtering must work without JavaScript** — server-render all items, filter by
  hiding → `astro-islands`.
- **Show 9–12, not everything.** Selection is the premium signal →
  `brand-narrative`.
- **Each card: one image, title, event type, location.** No excerpt — the image
  does the work → `art-direction`.
- **No pagination** at this scale. Pagination on twelve items signals a directory.

### Depth

**Every page reachable within three clicks of home**, and every case study
reachable from at least two places — the work index, and a related service page.
Orphan pages do not rank and are not found → `seo-foundations`.

### Redirects

If an existing site or links are being replaced:

```
# public/_redirects
/gallery        /work/                301
/portfolio      /work/                301
/index.php      /                     301
/contact-us     /contact/             301
```

**Ask the client for their existing URLs before launch.** Old Instagram bio
links, printed cards, and directory listings may point at paths that must keep
working → `local-discovery`.

### Caveats

- **This map is a proposal**, not a decision. It assumes a service mix that is
  still `[to verify]` → `project-event-catalogue`.
- **Fewer pages is harder to sell than more pages.** Clients equate page count
  with value. The argument is that each page must earn maintenance, and an
  abandoned page costs more than it ever returned.
- **If Marathi is added, this doubles** → `multilingual-content`. Decide before
  building routes.
- **The work index is the page most likely to need iteration** once there is real
  traffic. Build it so filtering can change cheaply.

### Checklist

- [ ] Page map agreed with the client, including what is excluded and why
- [ ] Each page has one stated job and one primary CTA
- [ ] URLs lowercase, hyphenated, trailing-slash consistent
- [ ] Case-study slugs include the year
- [ ] Five or fewer header items; no dropdowns
- [ ] Contact visually distinct in the header
- [ ] Sticky mobile contact bar present
- [ ] Footer carries full NAP, social, and privacy links
- [ ] Work index filters by event type and works without JavaScript
- [ ] Every page within three clicks; no orphans
- [ ] Existing URLs collected from the client and redirected
- [ ] `/thank-you/` noindexed
- [ ] Bilingual routing decided before routes are built

## References

- **`case-study-structure`, `enquiry-conversion`, `seo-foundations`,
  `static-deploy`, `astro`, `project-event-catalogue`** (this framework) — the
  decisions this map depends on and feeds
- **Nielsen Norman Group — information architecture and navigation depth**
  <https://www.nngroup.com/articles/flat-vs-deep-hierarchy/>
- **Google Search Central — site structure and internal linking**
  <https://developers.google.com/search/docs/crawling-indexing/links-crawlable>
- **Astro docs — routing and `trailingSlash`**
  <https://docs.astro.build/en/guides/routing/>

**Not sourced — written for this framework:** the nine-page map, the deliberate
exclusions and their reasoning, the one-job-per-page table, the work-index rules,
and the three-click/two-path depth requirement.
