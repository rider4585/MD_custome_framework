---
name: case-study-structure
version: 1.0.0
description: |
  The anatomy of a portfolio case study for an event — what sections exist, what
  each must prove, and the content model behind them. Use when writing or
  reviewing any piece of portfolio work, or when defining the CMS schema for
  events.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
---

## Case Study Structure

A gallery shows that an event happened. A case study shows that **this company
made it happen** — which is the thing being bought. The difference is the
presence of a problem and a decision.

Most event portfolios are galleries with a date on top. That is why they are
interchangeable.

> **Before running anything:** load `emotional-brief` for this event's register
> and `art-direction` for image roles. Structure carries the argument; images
> carry the feeling; both must agree.

### Method

1. **Find the problem.** Something went wrong or was hard. Rain, a venue change,
   a 400-guest jump, four hours' notice, a power cut, a family disagreement about
   the schedule. **If there is no problem, there is no case study** — publish it
   as a gallery instead and be honest about the distinction.
2. **Name the decision** the company made in response.
3. **State the outcome** in checkable terms.
4. **Lay the sections out** in the order below.
5. **Encode it as a schema** so every case study is comparably complete.

### The seven sections

| # | Section | Must prove | Length |
|---|---|---|---|
| 1 | **Hero** | This was beautiful | 1 image, ≤8-word title |
| 2 | **The brief** | We listened | 40–70 words |
| 3 | **The problem** | Real work happened | 40–80 words |
| 4 | **What we did** | We are competent, specifically | 80–150 words, 3–5 decisions |
| 5 | **The day** | The feeling | 9–14 images, minimal words |
| 6 | **In their words** | Someone else says so | 1 testimonial, ≤50 words |
| 7 | **The facts** | Scale and scope, checkable | A short table |

Sections 3 and 4 are what competitors will not write, because writing them
requires admitting something was difficult. **That is exactly why they work.**

### The facts table

Concrete numbers do more for credibility than any adjective. Every value must be
real; an invented guest count is discovered in the first venue conversation.

| Field | Example |
|---|---|
| Guests | 310 |
| Venue | Named, with permission |
| Duration | 2 days, 4 functions |
| Team on site | 14 |
| Vendors coordinated | 9 |
| Lead time | 5 weeks |

### Content model

Encode the structure so it cannot be half-filled. Worked example in Astro's
Content Layer (see `content-collections`); the *shape* is the point, not the
library.

```ts
// src/content.config.ts
import { defineCollection, z } from 'astro:content';
import { glob } from 'astro/loaders';

const events = defineCollection({
  loader: glob({ pattern: '**/*.md', base: './src/content/events' }),
  schema: ({ image }) => z.object({
    title:      z.string().max(60),
    eventType:  z.enum(['wedding','sangeet','mehendi','corporate','birthday','civic']),
    emotion:    z.enum(['reverence','exuberance','competence','delight','gravitas']),
    date:       z.coerce.date(),
    location:   z.string(),

    hero:       image(),
    heroAlt:    z.string().min(10),          // forces a real description

    brief:      z.string().min(150).max(500),
    problem:    z.string().min(150).max(600),
    decisions:  z.array(z.string()).min(3).max(5),

    gallery: z.array(z.object({
      src:  image(),
      alt:  z.string().min(10),
      role: z.enum(['establishing','hero','detail','human','scale','closing']),
    })).min(9).max(14),

    testimonial: z.object({
      quote:   z.string().max(280),
      author:  z.string(),
      consent: z.literal(true),              // cannot publish without it
    }).optional(),

    facts: z.object({
      guests:  z.number().int().positive().optional(),
      venue:   z.string().optional(),
      team:    z.number().int().positive().optional(),
      vendors: z.number().int().positive().optional(),
    }),

    mediaConsent: z.literal(true),           // see `media-consent`
    featured:     z.boolean().default(false),
  }),
});

export const collections = { events };
```

**The schema is the quality gate.** `min(9)` on the gallery, `min(150)` on the
problem, and `literal(true)` on consent mean an incomplete case study fails the
build rather than shipping thin. This is deliberate and it is the most valuable
part of this skill.

### Writing the sections

**The brief** — what the client asked for, in their terms. Resist improving it.

**The problem** — one specific difficulty, named plainly. Not "challenges arose".
"The venue withdrew eleven days out and the invitations were already printed."

**What we did** — three to five decisions, each a verb and an object. Not "we
managed the logistics" but "we moved the sangeet to the hotel lawn, kept the
original address on the invitation, and posted two people at the old venue to
redirect guests."

**Never blame the client, the venue, or a vendor.** Describe the situation, then
the response. A case study that blames reads as a company that will blame you.

### Caveats

- **Not every event yields a case study.** Ten strong ones beat forty thin ones;
  see `brand-narrative` on selection as a premium signal.
- **Client permission is required** for venue names, guest counts, family names,
  and photographs — separately for each. See `media-consent`.
- **The problem section needs client sign-off.** A family may not want "the venue
  cancelled" published. Ask; if refused, pick a different event.
- **Do not fabricate a problem.** It will not survive a reference call.

### Checklist

- [ ] A real problem identified and confirmed publishable with the client
- [ ] 3–5 decisions written as verb + object, not abstractions
- [ ] All seven sections present and within length guidance
- [ ] Facts table populated with verified numbers only
- [ ] 9–14 gallery images, each with an assigned role and real alt text
- [ ] Testimonial present with recorded consent
- [ ] Nobody blamed
- [ ] Content schema enforces completeness at build time
- [ ] Venue and family names cleared

## References

- **Astro docs — Content collections and the Content Layer API**, `defineCollection`,
  the `image()` schema helper, and Zod schemas as build-time validation
  <https://docs.astro.build/en/guides/content-collections/>
- **Nielsen Norman Group — how users read on the web** (scanning, front-loaded
  information) <https://www.nngroup.com/articles/how-users-read-on-the-web/>
- **Google Search Central — "Creating helpful, reliable, people-first content"**,
  on first-hand experience as a quality signal
  <https://developers.google.com/search/docs/fundamentals/creating-helpful-content>
- **Schema.org — `Event`**, the vocabulary the facts table maps onto for
  structured data <https://schema.org/Event> — see `structured-data`

**Not sourced — written for this framework:** the seven-section anatomy, the
"no problem, no case study" rule, the facts table fields, the schema-as-quality-
gate approach, and the never-blame rule.
