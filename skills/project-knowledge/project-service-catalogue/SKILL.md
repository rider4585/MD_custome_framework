---
name: project-service-catalogue
version: 2.0.0
description: |
  What this client actually sells, what each offering must convey, and how each
  maps to pages, features, and site or product structure. Use when planning
  information architecture, writing service or product pages, scoping features
  per offering, or deciding whether an offering deserves its own surface.
allowed-tools:
  - Read
  - Write
  - Grep
---

## Project: Service Catalogue

Structure is downstream of one question: **what does this client actually sell,
and which of those do they want more of?** Those are different lists, and the
work should be built for the second.

> ## 🟡 Template until onboarding fills it
>
> Shipped unfilled. Populated during onboarding → `project-discovery`.
>
> ## ⛔ Nothing here is confirmed until the client confirms it
>
> **No page, feature, or claim may ship for an offering the client has not
> confirmed they sell.** A service page is a promise to perform, and a feature
> built for an unsold offering is waste → `project-client-brand`.

### The catalogue

One row per offering. Fill from the client, not from a competitor's site.

| Offering | Sells as | Share of revenue | Wants more of? | Own surface? |
|---|---|---|---|---|
| — | — | — | — | — |

**Columns that decide things:**

- **Sells as** — standalone, add-on, or bundled. An add-on does not get a page.
- **Share of revenue** — ask for it. Clients routinely want to lead with the
  offering that pays least, because it is the one they enjoy.
- **Wants more of** — the strategic column. Build for this, not for the
  revenue column.
- **Own surface** — a page, a route, a feature area. Default to no.

### The tension to resolve with the client

**Clients almost always sell more than they want to be known for.** Presenting
everything equally is the weakest option available, because it says "available"
rather than "chosen", and available competes on price.

| Position | Consequence |
|---|---|
| **Single-offering brand** | Everything else becomes invisible and continues by word of mouth. Strongest positioning, some revenue anxiety |
| **Lead offering plus a catch-all** | The lead gets the surface; one page catches the rest. The safe default |
| **Everything, equally** | Weakest. Competes on price → `brand-narrative` |

**Recommend the middle unless the client has evidence for the first.** Then
defer; it is their revenue.

### The evidence rule

**An offering with fewer than two pieces of supporting evidence should not get
its own surface.** Case studies, worked examples, testimonials, screenshots,
data — whatever "evidence" means for this project. An empty page is a claim with
nothing behind it, and it converts worse than not having the page at all.

Record what evidence exists per offering, not what is planned →
`project-content-inventory`.

### Mapping an offering to the build

For each confirmed offering, record:

```yaml
offering: <name>
sells_as: standalone | add-on | bundled
own_surface: true | false
slug: /services/<the term the customer searches>/
evidence_count: 0
seasonality: <when it matters, if it does>
owner: <who at the client answers questions about it>
```

**Slugs use the customer's words, not the client's internal words.** Internal
vocabulary is one of the most reliable ways to become unfindable →
`seo-foundations`. Verify the terms; do not assume them.

### Caveats

- **This whole file is a hypothesis until the client confirms each row.** Ask;
  do not infer from a website, a directory, or a competitor.
- **Seasonality can dominate.** An offering with a three-month season will show
  almost no traffic or usage outside it — set that expectation before launch
  rather than explaining it afterwards.
- **"Wants more of" is aspirational by nature**, and building for it is a bet.
  That is legitimate, but say out loud that it is a bet, and make sure real work
  backs the surface before it ships.
- **Revenue share may be commercially sensitive.** If the client will not share
  it, ask them to rank rather than quantify.

### Checklist

- [ ] Every row confirmed with the client as sold / not sold / wants more of
- [ ] Revenue share or a ranking obtained
- [ ] A positioning stance chosen from the three, with the client
- [ ] No surface for an unconfirmed offering
- [ ] Every surface has at least two pieces of supporting evidence
- [ ] Slugs use verified customer terms, not internal jargon
- [ ] Seasonality recorded, and launch expectations set accordingly
- [ ] An owner named per offering

## References

- **`project-client-brand`, `project-context`** (this framework) — the facts and
  the goal this catalogue depends on
- **`project-content-inventory`, `project-site-architecture`,
  `brand-narrative`, `seo-foundations`** (this framework) — the decisions this
  catalogue feeds
- **Google Search Central — Creating helpful, people-first content**, on pages
  needing genuine supporting evidence
  <https://developers.google.com/search/docs/fundamentals/creating-helpful-content>

**Not sourced — written for this framework:** the catalogue columns and the
wants-more-of rule, the three positioning stances and the recommendation, the
two-pieces-of-evidence minimum, and the customer-vocabulary slug rule.
