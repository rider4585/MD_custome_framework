---
name: project-shoot-catalogue
version: 1.0.0
description: |
  The shoot types this studio sells, what each must convey, and how they map to
  service pages, case studies, and site structure. Use when planning information
  architecture, writing service pages, assigning an emotional register, or
  deciding whether a shoot type deserves its own page.
allowed-tools:
  - Read
  - Write
  - Grep
---

## Project: Shoot Catalogue

A photography studio's site structure is downstream of one question: **what does
this studio actually sell, and which of those does it want more of?** Those are
different lists, and the site should be built for the second.

> ## ⛔ The service list is unverified
>
> Nothing below is confirmed. The handle
> `@creative_weddings_films_latur` implies weddings and film in Latur, and that
> is the entire evidence base → `project-photographer-brand`.
>
> **No service page may be published for a shoot type the studio has not
> confirmed it sells.** A service page is a promise to perform.

### The candidate catalogue

Shoot types common to a wedding-and-function studio in this market. Treat every
row as a **question to ask**, not a service to build.

| Shoot type | Sells as | Register → `emotional-brief` | Own page? |
|---|---|---|---|
| **Full wedding (multi-day)** | The flagship | Reverence, with exuberance inside it | ✅ Always |
| **Wedding — single function** | Entry point | Per function | ⚠️ Only if genuinely booked alone |
| **Pre-wedding shoot** | High-volume, high-margin | Play, intimacy | ✅ Very likely — it is the most-searched term after "wedding photographer" |
| **Engagement / roka** | Add-on to a wedding booking | Anticipation | ✗ Section on the wedding page |
| **Wedding film** | A parallel product, not an add-on | Cinematic; its own register | ✅ If film is sold seriously → `film-showcase` |
| **Haldi / Mehendi / Sangeet** | Part of the package | Play / detail / exuberance | ✗ Beats within a case study → `wedding-story-arc` |
| **Reception** | Part of the package | Formality | ✗ |
| **Maternity** | Separate audience, separate season | Tenderness | ⚠️ Only if actively sold |
| **Newborn / naming ceremony** | Separate audience | Tenderness, stillness | ⚠️ Only if actively sold |
| **Birthday / family function** | Local volume work | Delight | ⚠️ Ask — often the bread and butter, often deliberately hidden |
| **Corporate event** | Off-season revenue | Competence | ⚠️ Ask. Rarely worth a page on a wedding-led site |
| **Portrait / fashion** | Portfolio-building, not revenue | Varies | ✗ |

### The tension to resolve with the client

**Studios almost always shoot more than they want to be known for.** A wedding
studio that also does birthdays, passport photos, and shop openings faces a
choice, and it is a positioning decision, not a design one → `brand-narrative`,
`signature-style`.

Three honest positions:

| Position | Site consequence |
|---|---|
| **Wedding-only brand** | Everything else is invisible. Referral work continues by phone. Strongest positioning, some revenue anxiety |
| **Weddings and functions** | Weddings lead; a single "other functions" page catches the rest. The safe default |
| **General studio** | Everything listed equally. Weakest — it competes on price → `signature-style` |

**Recommend position 2 unless the client has evidence for 1.** Then defer; it is
their revenue.

### The stills-and-film question

The handle says "films". If film is a real product rather than an occasional
extra, it changes the site fundamentally:

- **Film gets equal billing in navigation**, not a tab inside the wedding page.
- **Every case study carries a teaser**, and the film ladder applies →
  `film-showcase`.
- **The packages page is structured with film as an axis**, not an add-on line
  → `booking-and-packages`.
- **The signature must be defined twice** — stills and film are usually different
  → `signature-style`.

**This is the single highest-impact unknown in the project.** Resolve it before
the architecture is fixed → `project-site-architecture`.

### Mapping a shoot type to the site

For each confirmed type, record:

```yaml
shoot_type: pre-wedding
sells_as: standalone
register: play                    # from emotional-brief
own_page: true
slug: /services/pre-wedding-shoot/   # the term couples search, not the studio's term
case_studies: 3                   # minimum before the page is credible
film_offered: true
seasonality: "Nov–Feb peak"       # drives when the page matters
```

**A service page with fewer than two supporting case studies should not ship.**
An empty service page is a claim with no evidence, and it converts worse than not
having the page → `case-study-structure`.

### Naming

Use the words couples search, not the words the studio uses internally →
`seo-foundations`, `local-discovery`.

| Studio term | Page term |
|---|---|
| "Candid coverage" | Wedding photography |
| "Cinematography" | Wedding films |
| "Couple shoot" | Pre-wedding shoot |
| "Traditional coverage" | — (this is a package option, not a service) |

**Check the Marathi terms too** if the site is bilingual — the searched term in
Marathi is often not a translation of the English one → `multilingual-content`.

### Caveats

- **The entire table is a hypothesis about a market and a studio, both unverified.**
  Ask; do not infer.
- **Seasonality is severe in this market.** The wedding calendar is driven by
  auspicious dates, and a site launched in the off-season will show almost no
  traffic for months. Set that expectation before launch → `launch-review`.
- **The registers are inherited from `emotional-brief`**, which is itself an
  argued model tuned for event work. Re-test it against this studio's frames.
- **"Only if actively sold" is a judgement**, and the client may want a page for
  a service they hope to grow. That is legitimate — but it needs real work behind
  it before it ships.

### Checklist

- [ ] Every row confirmed with the client as sold / not sold / wants more of
- [ ] The stills-vs-film question resolved before architecture is fixed
- [ ] A positioning stance chosen from the three, with the client
- [ ] No service page for an unconfirmed service
- [ ] Every service page has at least two supporting case studies
- [ ] Slugs use searched terms, verified, not studio jargon
- [ ] A register assigned per confirmed type, tested against real frames
- [ ] Seasonality recorded, and launch expectations set accordingly
- [ ] Marathi terms checked if the site is bilingual

## References

- **`project-photographer-brand`** (this framework) — the unverified-facts
  register this catalogue depends on
- **`emotional-brief`, `signature-style`, `wedding-story-arc`,
  `film-showcase`, `booking-and-packages`, `project-site-architecture`**
  (this framework) — the decisions this catalogue feeds
- **Google Search Central — Creating helpful, people-first content**, on service
  pages needing genuine supporting evidence
  <https://developers.google.com/search/docs/fundamentals/creating-helpful-content>

**Not sourced — written for this framework:** the candidate catalogue and its
own-page judgements, the three positioning stances and the recommendation, the
stills-vs-film escalation, the two-case-study minimum, and the naming table.
