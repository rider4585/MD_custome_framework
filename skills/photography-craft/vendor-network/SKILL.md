---
name: vendor-network
version: 1.0.0
description: |
  Build the referral network that actually books a wedding photographer —
  planners, venues, decorators, makeup artists — and make the website serve it.
  Use when designing credits, a venues page, or a vendor-facing surface, or when
  the site gets traffic but the bookings all come by word of mouth anyway.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
---

## Vendor Network

Most wedding photographers get most of their work from other wedding vendors, not
from search. A planner who has worked with a photographer three times will
recommend them without being asked; a couple who found a site through Google is
comparing five studios on price. **The referral is the highest-value channel and
the one websites are usually designed to ignore.**

This skill is about designing the site for a second audience — vendors — without
compromising the first.

> **Before running anything:** load `project-photographer-brand` for the vendors
> this studio actually works with. **Never list a venue or vendor the studio has
> not worked with**; it is a false claim of association and vendors notice.

### Method

1. **Map the real network** from past weddings — who was on each job.
2. **Give credit generously and specifically** on every case study →
   `image-rights-and-credit`.
3. **Build the two vendor-serving surfaces**: a venues page and a share-back
   pipeline.
4. **Make sharing frictionless** — this is the mechanism that compounds.
5. **Measure referral source** on the enquiry form → `booking-and-packages`.

### Why credit is the mechanism

A wedding vendor's own portfolio problem is that **they rarely have good
photographs of their own work.** A decorator has a phone snap of a mandap they
spent three weeks building; the photographer has the frame that makes it look
like what they intended.

That asymmetry is the whole opportunity. A photographer who reliably sends a
decorator ten graded, credited frames within a week of the wedding becomes the
photographer that decorator names first, forever. It costs an hour.

**The site's job is to make that exchange visible and repeatable:**

- Every case study credits every vendor by role, name, and link.
- A short, honest note on the process page: "We share images with the vendors on
  every wedding, credited, at no cost."
- A vendor-facing page describing how to request images and what the usage terms
  are.

### The share-back pipeline

The operational half. Without it the credit is decorative.

1. **Record vendors during the job**, not months later — a field in the shoot
   record, filled on the day.
2. **Within 7 days** of delivery, send each vendor 8–12 frames featuring their
   work, web-sized, with IPTC credit embedded → `image-rights-and-credit`.
3. **State the terms in the same message**, in one sentence: free to use on their
   own channels, credit required, no cropping out the credit, no resale, no use
   in paid advertising without asking.
4. **Check consent first.** Vendor sharing is a publication. A couple who
   consented to the studio's portfolio has not automatically consented to a
   decorator's Instagram → `media-consent`. Make this an explicit line item in
   the consent form.
5. **Log what was sent to whom.** It is the record when a usage question arises.

**The consent step is the one most studios skip**, and it is the one that creates
real exposure under DPDP-style regimes, because the studio is the party that
disclosed the data.

### The venues page

The single highest-return SEO page a wedding photographer can build, because it
matches how couples actually search: **venue name plus "photographer"**.

- **One page per venue the studio has genuinely shot at**, not a list.
- **Real content per venue**: how the light behaves and when, the rooms that work
  and the one that does not, the timing constraint everyone gets wrong, where the
  ceremony is usually set, and 6–10 frames from real weddings there.
- **This is knowledge only the photographer has**, which makes it the rare page a
  competitor cannot copy and an AI cannot generate → `seo-foundations`.
- **Do not imply endorsement** by the venue. "We have photographed nine weddings
  here" is a fact; a venue logo wall implies a partnership that may not exist.
- **Send the page to the venue.** Venues link to useful pages about themselves,
  and that link is worth more than the page's own ranking.

**Do not build this page for venues the studio has not worked at** to catch the
search traffic. It is thin content, it is a false claim, and the venue will see it.

### What not to do

- **Reciprocal link pages.** "Our partners" grids of logos, mutually linked. They
  convert nothing and look like a link scheme → `seo-foundations`.
- **Paid directory listings as a strategy.** Occasionally worth it for local
  discovery; never a substitute for the network → `local-discovery`.
- **Exclusivity deals with a single planner.** They feel like security and they
  cap the studio's ceiling at that planner's volume.
- **Crediting a vendor who was not there** because it might flatter them into a
  referral. It is a lie, and the industry is small.
- **Chasing referrals from competitors' clients.** Photographers refer overflow
  work to each other constantly — that network is real, and it is built by being
  someone who returns the favour, not by asking.

### The overflow relationship

Worth naming because it is the most under-used channel in the whole industry:
**other photographers.** Every working studio turns down dates it is already
booked on. Where those enquiries go is decided almost entirely by who the studio
trusts and remembers.

The site can support this directly: a short line on the contact page — "Date
already booked? We will happily point you to photographers we trust" — and an
actual list the studio maintains privately. It costs a booking they could not
have taken anyway and buys reciprocity.

### Measuring it

| Signal | Where |
|---|---|
| "How did you hear about us" on the enquiry form | The only direct measure. One optional field → `booking-and-packages` |
| Referral traffic from vendor domains | Analytics, by source |
| Venue page → enquiry rate | Per page, not aggregate |
| Vendors who received images vs. vendors who referred | The share-back log. This is the number that tells you whether the pipeline works |

Expect the honest finding that a large share of bookings have no traceable web
source. That is not a measurement failure — it is the network working, and it is
the argument for investing in it.

### Caveats

- **Referral norms vary by market.** In some regions vendor kickbacks or
  commission-sharing are common. This framework does not recommend paid referral
  arrangements — they distort the recommendation the couple receives — but the
  studio may operate in a market where they are the norm. If they do, it should
  be disclosed, and that is their decision to make.
- **Usage terms sent by email are weak.** They are workable for goodwill sharing
  and inadequate for anything commercial. A written licence is the right
  instrument if a vendor wants images for paid advertising →
  `image-rights-and-credit`.
- **Venue pages age.** Renovations, new management, and changed rules make the
  practical detail wrong within a couple of years. Date them and review annually.
- **The claim that referrals dominate this market is a strong generalisation.**
  It is well-established as industry lore rather than measured; verify it against
  this studio's own booking history before building strategy on it.

### Checklist

- [ ] Real vendor network mapped from past jobs, not assumed
- [ ] Every case study credits every vendor by role, name, and link
- [ ] No vendor or venue listed that the studio has not worked with
- [ ] Share-back pipeline defined with a 7-day target and a log
- [ ] Consent for vendor sharing captured explicitly in the consent form
- [ ] Usage terms stated in every share-back message
- [ ] Venue pages built only for venues actually shot, with real photographer's
      knowledge and real frames
- [ ] Venue pages avoid implying endorsement, and are sent to the venue
- [ ] No reciprocal link grid
- [ ] Overflow referral line on the contact page, with a maintained list behind it
- [ ] Referral source captured on the enquiry form
- [ ] Share-back log compared against actual referrals at least once a year

## References

- **This framework's `image-rights-and-credit`** — the embedded credit metadata
  and licence terms that make share-back safe
- **This framework's `media-consent`** — why vendor sharing is a separate
  disclosure requiring its own consent under India's DPDP Rules 2025
- **This framework's `local-discovery` and `seo-foundations`** — where the venue
  pages fit in the discovery strategy
- **Google Search Central — Link spam policies**, on reciprocal linking and
  link schemes
  <https://developers.google.com/search/docs/essentials/spam-policies#link-spam>
- **Google Search Central — Creating helpful, people-first content**, the
  first-hand-experience criterion the venue pages are built to satisfy
  <https://developers.google.com/search/docs/fundamentals/creating-helpful-content>

**Not sourced — written for this framework:** the asymmetry argument for why
credit works, the five-step share-back pipeline and its 7-day target, the venue
page specification, the overflow-referral channel, the anti-patterns, and the
measurement table.
