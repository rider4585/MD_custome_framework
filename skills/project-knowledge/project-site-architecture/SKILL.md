---
name: project-site-architecture
version: 2.0.0
description: |
  The page map, URL structure, and navigation for this project, and the
  reasoning behind each. Use when adding a page, deciding where content
  belongs, defining routes and redirects, or when the page count keeps growing.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## Project: Site Architecture

Most client sites need **fewer pages than anyone expects**. Every additional page
dilutes the work, adds maintenance, and gives a visitor another way to not reach
the one action the site exists to produce.

> ## 🟡 Template until onboarding fills it
>
> Shipped unfilled. Populated during onboarding → `project-discovery`.
>
> **Before filling it:** load `project-service-catalogue` — the offering pages
> depend on offerings that may not be confirmed — and `project-context` for the
> single action this site exists to produce.

### Page map

```
/                     Home
/<work>/              Index of the primary evidence
/<work>/<slug>/       One piece of evidence      (N of these)
/services/<slug>/     One offering               (only for confirmed offerings)
/about/               Who this is
/contact/             The primary action
/thank-you/           Post-action                (noindex)
/privacy/             Obligations, erasure contact
/404                  Not found
```

Add to this only what the project's own answers justify. Every page below is a
question, not a default:

| Candidate page | Add it when |
|---|---|
| Pricing | Customers search for price, or hiding it wastes enquiry capacity |
| Process | The customer has never bought this before and uncertainty stalls them |
| Blog | Someone has committed to maintaining it. Otherwise never |
| FAQ | Real enquiries reveal genuinely repeated questions |
| Testimonials page | Almost never — quotes belong beside the work they describe |
| Separate gallery | Almost never — evidence without a story is what competitors have |
| Partner/logo wall | Almost never — converts nothing, reads as a link scheme |

**Record what was excluded and why, next to what was included.** The exclusions
are the decisions; the inclusions are usually obvious. Clients equate page count
with value, so the argument that each page must earn its maintenance has to be
made explicitly, once, early.

### What each page must do

Every page gets exactly one job and one primary action. A page with two jobs has
none.

| Page | One job | Primary CTA |
|---|---|---|
| — | — | — |

### URL rules

- **Lowercase, hyphenated, no parameters.**
- **Trailing slashes, consistently** — pick one and enforce it at the host.
- **Slugs use the customer's vocabulary**, not internal vocabulary →
  `seo-foundations`, `project-service-catalogue`.
- **Include a disambiguator where entries repeat** — a year, a client, a region.
- **Never change a slug after launch** without a 301.
- **Anything private lives on a separate host**, not a route inside this tree.
  Route-level separation is one misconfigured redirect from exposure.

### Navigation

**Five or six items maximum in the header.**

- **The evidence first.** It is the reason to be on the site.
- **The primary action visually distinct** — a button, not a link.
- **No dropdowns** at this size. A persistent accessibility cost for no gain →
  `accessibility`.
- **Footer carries** the full contact block, social links, legal pages, and any
  existing-customer entry point.

**Header is for prospects; footer is for everyone else.** Pages that serve
existing customers, or that only matter once someone is already convinced, go in
the footer and in contextual links.

### Depth

**Every page reachable within three clicks of home**, and every leaf page
reachable from **at least two places**. A second path is what makes a section
compound rather than sit at the end of one branch. Orphan pages do not rank and
are not found → `seo-foundations`.

### Redirects

If an existing site or existing links are being replaced:

```
/old-path       /new-path/       301
```

**Ask the client for their existing URLs before launch.** Printed material,
social bio links, directory listings, and email signatures may point at paths
that must keep working, and nobody remembers them all — pull the list from
analytics and the old server logs as well as from memory.

### Caveats

- **This map is a proposal, not a decision.** It assumes an offering mix that
  may still be unconfirmed → `project-service-catalogue`.
- **Fewer pages is harder to sell than more pages.** Make the maintenance
  argument once, early, and record the client's answer.
- **Multilingual doubles this.** Decide before building routes, not after →
  `multilingual-content`.
- **The index page is the one most likely to need iteration** once there is real
  traffic. Build it so filtering and ordering can change cheaply.

### Checklist

- [ ] The single action this site exists to produce is named
- [ ] Page map agreed with the client, including what is excluded and why
- [ ] Each page has one stated job and one primary CTA
- [ ] URLs lowercase, hyphenated, trailing-slash consistent
- [ ] Slugs use verified customer vocabulary
- [ ] Six or fewer header items; no dropdowns
- [ ] Prospect pages in the header, customer pages in the footer
- [ ] Every leaf page reachable from two places; no orphans
- [ ] Nothing private inside this route tree
- [ ] Existing URLs collected from analytics, logs, and the client, and redirected
- [ ] Post-action page noindexed
- [ ] Multilingual routing decided before routes are built

## References

- **`project-context`, `project-service-catalogue`, `project-content-inventory`**
  (this framework) — the decisions this map depends on
- **`seo-foundations`, `accessibility`, `enquiry-conversion`,
  `multilingual-content`** (this framework) — the decisions it feeds
- **Nielsen Norman Group — flat vs. deep hierarchies**
  <https://www.nngroup.com/articles/flat-vs-deep-hierarchy/>
- **Google Search Central — site structure and crawlable links**
  <https://developers.google.com/search/docs/crawling-indexing/links-crawlable>

**Not sourced — written for this framework:** the default page map and the
candidate-page table, the record-the-exclusions rule, the one-job-per-page
requirement, the header-vs-footer split, and the two-path depth requirement.
