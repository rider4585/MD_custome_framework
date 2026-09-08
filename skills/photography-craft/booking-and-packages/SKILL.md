---
name: booking-and-packages
version: 1.0.0
description: |
  Present packages, pricing, and availability on a photographer's site so the
  right couples enquire and the wrong ones self-select out. Use when building a
  pricing or investment page, when the client asks whether to show prices, or
  when enquiry volume is high and booking rate is low.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
---

## Booking and Packages

A wedding photographer's site has one commercial job: **produce a small number of
enquiries from couples who are a good fit, on dates that are free.** Volume is
not the goal — every unqualified enquiry costs the studio an hour of replying and
the couple a disappointment.

The single decision that governs this page is whether to publish prices. Most
studios avoid it, and most of them are wrong about why.

> **Before running anything:** load `project-photographer-brand` for the real
> package structure and prices. **Never invent a price, a package name, or an
> availability claim.** A wrong number on a pricing page is a commercial and
> reputational problem, not a placeholder.

### Method

1. **Decide the pricing disclosure level** using the ladder below.
2. **Structure packages around what a couple must choose**, not around what the
   studio delivers internally.
3. **Name the qualifying constraints** — date, travel, coverage hours.
4. **Design the enquiry form to qualify**, not to collect → `enquiry-conversion`.
5. **Publish the process**, because uncertainty is the main reason a couple
   hesitates.
6. **Measure enquiry-to-booking rate**, not enquiries.

### The pricing disclosure ladder

| Level | What is shown | Effect |
|---|---|---|
| **0 — Nothing** | "Contact for pricing" | Maximum enquiry volume, minimum quality. Every couple must send an email to learn they cannot afford it |
| **1 — Starting from** | "Wedding coverage from ₹X" | The best default for most studios. Filters the bottom out; keeps negotiating room |
| **2 — Package tiers** | Three named tiers with prices | Highest qualification. Right for a studio with a settled, repeatable offer |
| **3 — Full price list** | Every line item, including add-ons | Rare. Suits high-volume, standardised operations; reads as a commodity for premium work |

**Recommend level 1 or 2.** Level 0 is chosen out of a fear that competitors will
undercut, but competitors already know the market rate — the only person the
hidden price stops is the couple. A "from" figure is the cheapest qualification
mechanism available.

**The counter-argument is real and should be stated to the client:** a published
price anchors the negotiation and can lose a client who would have paid more
after falling in love with the work. If they choose level 0, honour it and
compensate with a strong process page and a qualifying form.

### Structuring packages

Three tiers, and the middle one should be the one most couples choose. Differ
them on **one primary axis** — usually hours of coverage or number of shooters —
plus one or two secondary items. Tiers that differ on six axes at once cannot be
compared, so nobody chooses.

| Element | Guidance |
|---|---|
| **Number of tiers** | Three. Two reads as a bait-and-switch, four or more paralyses |
| **Primary axis** | Coverage hours, or stills-only vs. stills + film |
| **What to name** | The deliverable a couple can picture: "8 hours, two photographers, 400+ edited images, online gallery" |
| **What not to name** | Internal process: "professional editing workflow", "backed-up archive". They assume it |
| **Film** | An axis, not a tier. A couple choosing film is making a different decision → `film-showcase` |
| **Albums and prints** | Add-ons, listed after tiers. Bundling them inflates the headline price |
| **Travel** | A stated rule, not a per-enquiry mystery: "Within 150 km included; beyond that, travel and stay at cost" |

**Every tier must state what is *not* included.** The most expensive support cost
in this business is a couple who believed something was in the package.

### Availability

Dates are the hard constraint, and the earliest disqualifier.

- **Ask for the date first** in the enquiry form. It costs the couple two seconds
  and lets the studio answer in one line if the date is gone.
- **Publish the booking horizon** — "currently booking for 2027" — which is both
  a qualifier and a status signal.
- **A live availability calendar is usually a mistake.** It is more maintenance
  than it looks, it goes stale the moment it is wrong, and a stale calendar that
  shows a booked date as free produces the worst possible conversation. Only ship
  one if it is wired to the studio's real calendar and the studio has committed
  to keeping it accurate.
- **Say how many weddings are taken per year** if the number is deliberately
  small. Scarcity that is true is the most credible kind → `brand-narrative`.

### The process page

A couple has typically never hired a photographer before. Uncertainty, not price,
is what stalls the enquiry. A short, concrete process page converts:

1. **Enquire** — what to send, what comes back, and how fast ("within 48 hours").
2. **Call or meet** — how long, in person or video, and what gets decided.
3. **Book** — the deposit percentage, what the contract covers, when the balance
   is due.
4. **Before the day** — the planning conversation, the timeline, the shot
   conversation, the venue visit if there is one.
5. **The day** — arrival time, what the photographer needs, how they work
   (this is where the refusal from `signature-style` belongs).
6. **After** — teaser turnaround, full gallery turnaround, how delivery works
   → `client-gallery-delivery`.

**Publish real turnaround times, and beat them.** A studio that promises six
weeks and delivers in four generates referrals; one that promises two and
delivers in eight loses them → `vendor-network`.

### The enquiry form

The form is a qualifier. Every field must either disqualify or enable a better
first reply.

| Field | Keep? | Why |
|---|---|---|
| Date | ✅ Required, first | The hardest constraint |
| Venue / city | ✅ | Travel, light, logistics |
| Which functions | ✅ | A Haldi-only enquiry is a different job → `wedding-story-arc` |
| Stills / film / both | ✅ | Routes the reply |
| Budget range | ⚠️ | Improves qualification; suppresses submissions. Optional, with ranges, never a free-text number |
| How they found the studio | ⚠️ | Useful once; drop it if it costs a submission |
| "Tell us about your day" | ✅ | One open field. The best signal of fit in the whole form |
| Phone | ✅ | In this market most bookings are closed on a call |
| Anything else | ✗ | Ask on the call |

Accessible labels, real `autocomplete` attributes, one column, no multi-step
wizard, and a confirmation that states what happens next and by when →
`enquiry-conversion`, `accessibility`.

### Anti-patterns

- **"Investment" as a euphemism.** Couples searching for pricing search for
  "pricing". Name the page accordingly, whatever the heading says →
  `seo-foundations`.
- **A PDF price list behind an email gate.** It adds a step, and the PDF is
  immediately out of date.
- **Prices without dates.** A published price with no year becomes wrong silently.
- **Fake scarcity.** "Only 2 dates left!" on a site that has said it for a year.
- **Packages named Silver, Gold, Platinum.** They describe nothing; the couple
  still has to read all three.
- **Hiding the travel rule** so it can be applied case by case. It reads as
  improvised when it surfaces.

### Caveats

- **Every number on this page belongs to the client.** This skill structures the
  presentation; it does not set prices, and an agent must never estimate one.
- **Market norms vary enormously by region and community.** What qualifies as a
  premium price in Latur is not what it is in Mumbai; do not import assumptions.
- **Level 0 can be correct** for a studio doing very high-value bespoke work with
  no standard offer. Argue for disclosure, then defer.
- **The three-tier structure is a convention, not a law.** A studio with one
  honest offer should show one.
- **This is not legal or tax advice.** Contract terms, deposits, cancellation,
  and GST treatment are the studio's and their advisor's call.

### Checklist

- [ ] Disclosure level chosen deliberately, with the trade-off explained to the client
- [ ] Every price, package, and inclusion verified with the client — nothing invented
- [ ] Tiers differ on one primary axis; the intended middle tier is identifiable
- [ ] Exclusions stated for every tier
- [ ] Travel rule published
- [ ] Booking horizon stated; no stale availability calendar shipped
- [ ] Process page published with real turnaround times
- [ ] Enquiry form asks the date first and every field justified
- [ ] Confirmation states what happens next and by when
- [ ] Page findable by the word couples actually search for
- [ ] Prices dated, with a review reminder
- [ ] Enquiry-to-booking rate measured, not enquiry count

## References

- **This framework's `enquiry-conversion`** — form accessibility, spam handling,
  and the confirmation pattern this skill builds on
- **Baymard Institute — form usability research**, on field count and abandonment
  <https://baymard.com/blog/checkout-flow-average-form-fields>
- **Nielsen Norman Group — "The Paradox of Choice in UX"**, on why three
  comparable options outperform many
  <https://www.nngroup.com/articles/psychology-study-guide/>
- **WCAG 2.2 — SC 1.3.5 Identify Input Purpose, SC 3.3.2 Labels or Instructions**
  <https://www.w3.org/TR/WCAG22/>

**Not sourced — written for this framework:** the four-level disclosure ladder
and its recommendation, the one-primary-axis tier rule, the availability-calendar
warning, the six-step process page, the field-by-field form table, and the
anti-patterns.
