---
name: project-photographer-brand
version: 1.0.0
description: |
  The verified facts about the photography studio this site is for, and — more
  importantly — the list of what is NOT yet verified. Use before writing any
  copy, publishing any claim or price, or entering any business detail into
  structured data. Load this first, in every session that touches content.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
---

## Project: Photographer Brand Facts

This is the project's **single source of truth about the client**. It exists
because the alternative — an agent inferring plausible business facts — produces
copy that is confidently wrong, and a wrong address, price, or credit propagates
across the web and into the studio's reputation.

> ## ⛔ Phase 0 is blocking
>
> **Nothing marked `[to verify]` may be published.** Not in copy, not in
> structured data, not in a meta description, and above all not in a price.
> An agent that needs an unverified fact must either omit it or escalate —
> never estimate.
>
> This is the rule most likely to be argued with, and the one that protects the
> client most.

### Status

| | Count |
|---|---|
| Verified with the client | **1** |
| `[to verify]` — inferred, not confirmed | 3 |
| Unknown — must be asked | 24 |

**As of 2026-09-08, only the Instagram handle has been supplied by the client
side.** Everything else in this file is inference or a question. Update this
table when that changes.

### Verified

| Fact | Value | Confirmed | How |
|---|---|---|---|
| Instagram handle | [`@creative_weddings_films_latur`](https://www.instagram.com/creative_weddings_films_latur/) | 2026-09-08 | Supplied directly by the project owner in session |

That is the entire verified set. Do not add to it without a date and a method.

### `[to verify]` — inferred from the handle alone

**These are inferences from a username. They are the weakest possible evidence
and are recorded here so that nobody re-derives them and mistakes them for
research.**

| Inference | Value | Basis | Risk if wrong |
|---|---|---|---|
| Trading name contains "Creative" | e.g. "Creative Weddings & Films" | Handle | Wrong name in `LocalBusiness` markup and every page title |
| Operating location | Latur, Maharashtra | Handle | Wrong `areaServed`, wrong local SEO strategy, wrong venue pages |
| Services include weddings **and** film | Stills + video | Handle contains "films" | The whole site structure — film-selling and stills-selling sites differ → `film-showcase` |

**The trading name is the highest-risk item.** It goes into the page title of
every page, the `LocalBusiness` name, the OG tags, and the copyright line in
every image's IPTC metadata. Getting it wrong is expensive to unwind →
`structured-data`, `image-rights-and-credit`.

### The Instagram gap

**The grid could not be read.** Instagram serves a login wall to automated
fetching; a fetch of the profile on 2026-09-08 returned no bio, no captions, no
counts. A web search for the handle and for "Creative Weddings Films Latur"
returned **no corroborating listing** — no Justdial, Sulekha, WeddingWire, or
directory entry was found.

So: **the archive is unassessed and the business is undocumented in public
sources.** This is a larger gap than the Eventina project had, and it is
blocking for content work.

**A human with account access must supply:**

- The bio text, display name, and link in bio
- Follower count and posting cadence
- **A breakdown of the last 30–50 posts**: which are stills, which are reels,
  which are full films, and what function each covers → `wedding-story-arc`
- **Whether the reels are the studio's own edits** or client-supplied
- 100+ frames the photographer chose themselves, for the signature tally →
  `signature-style`
- Which posts performed best, and the photographer's own view of why

Until then, `project-content-inventory` treats the archive as unassessed, and no
agent may characterise the studio's style, service mix, or standard in copy.

### Unknown — must be asked

Nothing here can be guessed.

**Identity and business**
1. Exact trading name and its spelling, capitalisation, and any ampersand
2. Legal entity name and registration status
3. Who the photographer is — name, and whether the brand is personal or a studio
4. Studio address, if there is a public one, and whether it should be published
5. Phone number(s) that are answered, and by whom
6. Business email
7. Whether a domain is owned, and by whom
8. Year they started shooting weddings
9. Team size — solo, or a team; who shoots, who edits, who films

**The work** → `signature-style`, `project-shoot-catalogue`
10. Stills, film, or both — and which is the primary product
11. Weddings per year, and how many they *want*
12. Which functions they shoot, and which they decline
13. What they refuse — the shot or job they will not take
14. Do they cut teasers as well as highlight films? → `film-showcase`
15. Typical delivery turnaround, actual not aspirational
16. Second shooters and film team — names, and whether copyright is assigned
    → `image-rights-and-credit`

**Commercial** → `booking-and-packages`
17. Package structure and real prices
18. Whether prices may be published, and at what disclosure level
19. Travel rule — included radius, and charges beyond it
20. Booking horizon — which year they are currently booking
21. Deposit terms and turnaround commitments

**Delivery and rights** → `client-gallery-delivery`, `media-consent`
22. How galleries are delivered today, and on what platform
23. Whether written client consent for marketing use exists, and in what form
24. Music licensing for published films — libraries used, licence IDs held
    → `film-showcase`

**Market and language** → `multilingual-content`
25. Marathi, English, or both — with evidence, not assumption
26. Venues they have actually shot at → `vendor-network`
27. Vendors they work with regularly

### How to use this file

**Reading:** load it at the start of any session that writes copy, metadata,
prices, or structured data.

**Writing:** when the client confirms a fact, move it into the **Verified**
section with the date and how it was confirmed:

```markdown
| Phone | +91 XXXXX XXXXX | 2026-09-15 | Call with <name>, noted in project log |
```

**Never delete a fact that turns out to be wrong** — record the correction, so
the same wrong value is not re-derived from the same source later.

### Escalation

When an agent needs an unverified fact:

1. **Omit it** and continue, if the work can proceed without it.
2. **Mark the gap** in the output — `[NEEDS: verified trading name]`.
3. **Escalate** to the orchestrator with `query`, naming the fact and why it is
   blocking.

**Do not**: estimate, use an inference as if verified, invent a price, or write
copy around the gap so smoothly that nobody notices it is missing.

### Checklist

- [ ] Loaded before any copy, metadata, price, or structured data is written
- [ ] No `[to verify]` value published anywhere
- [ ] Trading name confirmed before it enters page titles or IPTC metadata
- [ ] Prices confirmed by the client; never estimated
- [ ] Instagram gap filled by a human with account access
- [ ] Signature derived from real frames, not from the handle
- [ ] Every confirmation recorded with date and method
- [ ] Corrections recorded, not silently overwritten
- [ ] Status counts at the top kept current

## References

- **Instagram — [`@creative_weddings_films_latur`](https://www.instagram.com/creative_weddings_films_latur/)**,
  supplied by the project owner 2026-09-08. **The profile and grid were not
  readable** — an automated fetch returned only the login wall
- **Web search, 2026-09-08** — no directory or listing corroborating this studio
  was found; the Latur wedding-vendor listings that surfaced
  ([Sulekha](https://www.sulekha.com/wedding-videographers/latur),
  [5BestInCity](https://ind.5bestincity.com/wedding-photographers-in-latur-mh))
  name other studios and are recorded here only as evidence of the search, not
  as facts about this client
- **`SOURCES.md`** (this repository) — the consolidated record of what was
  gathered, when, and from where

**Not sourced — written for this framework:** the Phase 0 blocking rule, the
verified/`[to verify]`/unknown three-state model, the escalation procedure, the
inference-risk table, and the list of questions to ask.
