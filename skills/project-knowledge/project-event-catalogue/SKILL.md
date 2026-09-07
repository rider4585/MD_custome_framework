---
name: project-event-catalogue
version: 1.0.0
description: |
  The event types Eventina sells, what each must convey, and how they map to
  services and site structure. Use when planning the site's information
  architecture, writing service pages, or assigning an emotional register to a
  case study.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
---

## Project: Event Catalogue

The catalogue drives three things: the **services** the site sells, the
**emotional registers** the design must support, and the **taxonomy** in the
content schema. Getting it wrong means rebuilding the content model after
content exists.

> **Before running anything:** load `project-eventina-brand`. Everything below is
> `[to verify]` — assembled from public listings, not confirmed by the client.

### Status

`[to verify]` — the only public evidence is a directory listing naming
**weddings, birthday parties, and dance parties**. Everything else here is
inferred from the market and **must be confirmed before it reaches a service
page**.

### Proposed catalogue

| Event type | Evidence | Register → `emotional-brief` | Site treatment |
|---|---|---|---|
| **Wedding — ceremony** | `[to verify]` Justdial | Reverence | Flagship case studies |
| **Sangeet / Mehendi / Haldi** | Inferred | Exuberance | Often within a wedding case study, not separate |
| **Reception** | Inferred | Exuberance → reverence | Part of the wedding story |
| **Birthday / private party** | `[to verify]` Justdial | Delight | Secondary; shows range |
| **Corporate event** | Unknown — **ask** | Competence | Distinct page if they want this work |
| **Public / civic function** | Unknown — **ask** | Gravitas | Distinct page if they want this work |

**The wedding is one event or many, and this is a real modelling decision.** An
Indian wedding spans several functions across days, each with a different
register. Two options:

| Model | Pros | Cons |
|---|---|---|
| **One case study per wedding**, functions as sections | Tells a whole story; matches how clients buy | Long pages; registers must shift within one page |
| One case study per function | Each has a clean register | Fragments the story; repeats the client |

**Recommend one case study per wedding**, with functions as sections — matching
how a family actually buys. This is why `emotional-brief` insists chrome stays
neutral: a single page must hold reverence and exuberance without breaking →
`editorial-layout`.

### Taxonomy for the content schema

```ts
eventType: z.enum(['wedding','sangeet','mehendi','corporate','birthday','civic'])
```

**Do not extend this list without updating** `content-collections`, the CMS
config in `git-cms`, and the register table in `emotional-brief`. All three are
descriptions of the same taxonomy and they drift.

### Services versus events

Two different things, and conflating them makes a confusing site.

- **Event types** are what happened — the taxonomy on case studies.
- **Services** are what is sold — what a client buys.

A services page structured around what is sold, each linking to the case studies
that demonstrate it → `seo-foundations` on cross-linking.

| Candidate service | Status |
|---|---|
| Full wedding planning | `[to verify]` — likely core |
| Day-of coordination | Unknown — ask |
| Decor and stage design | Unknown — ask |
| Vendor coordination | Unknown — ask |
| Corporate event management | Unknown — ask |
| Destination events | Unknown — ask |

**Do not publish a service the client does not offer.** It generates enquiries
they must turn down, which is worse than no enquiry.

### Questions for the client

1. Which of the six event types do you actually take? Which do you want more of?
2. Which do you want *less* of? (the refusal → `brand-narrative`)
3. What is the split by volume, and by revenue? They usually differ
4. Is a wedding sold as one engagement or per function?
5. What services would you list, in your own words?
6. Do you take work outside Latur? How far?
7. Is there work you have done that is not on Instagram?

### Caveats

- **This catalogue is inference from two directory keywords.** Treat every row as
  a hypothesis. Confirm before building service pages.
- **Volume and prestige diverge.** The client may run mostly birthdays and want
  to be known for weddings. The site should reflect ambition *and* the archive
  should support it → `photo-curation`.
- **A taxonomy is expensive to change** once content exists. Spend the time now.
- **Marathi/Hindi naming matters** — *sangeet*, *haldi*, *mehendi*, *vidai* are
  the terms clients use and search for. Use them → `web-copywriting`.

### Checklist

- [ ] Every event type confirmed with the client, not inferred
- [ ] Wedding modelled as one case study or several — decided and recorded
- [ ] Taxonomy identical across schema, CMS config, and register table
- [ ] Services distinguished from event types
- [ ] No service published that the client does not offer
- [ ] Volume/prestige divergence discussed
- [ ] Local-language event names used in copy
- [ ] Geographic service area confirmed

## References

- **Justdial — Eventina Organisers listing**, the only public evidence of the
  service mix, recorded as `[to verify]`
  <https://www.justdial.com/Latur/Eventina-Organisers-Near-Manas-Hotel-Latur-HO/9999P2382-2382-191109130517-W8Y2_BZDET>
- **`emotional-brief`, `content-collections`, `git-cms`, `brand-narrative`**
  (this framework) — the three places this taxonomy must stay in sync, and the
  positioning work that depends on it
- **`project-eventina-brand`** (this framework) — the verification status of
  every fact used here

**Not sourced — written for this framework:** the proposed catalogue and its
register mapping, the one-case-study-per-wedding recommendation, the
services-versus-events distinction, and the client questions.
