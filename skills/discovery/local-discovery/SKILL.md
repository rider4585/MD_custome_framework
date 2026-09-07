---
name: local-discovery
version: 1.0.0
description: |
  Get a local service business found where its customers actually look — Google
  Business Profile, NAP consistency, directory listings, and reviews. Use when
  planning discovery for a location-based business, at launch, or when the site
  gets traffic but no enquiries from search.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## Local Discovery

For a wedding planner in Latur, **the website is not the primary discovery
channel and pretending otherwise wastes the client's money.** The realistic order
is roughly:

1. **Word of mouth** — by a distance. The site's job here is to confirm a
   recommendation, not to create one
2. **Instagram** — where the work is already being seen
3. **Google Business Profile / Maps** — "wedding planner near me"
4. **Directory listings** — Justdial, Sulekha, WeddingWire in this market
5. **Organic web search** — last, and slowest to build

The site supports all five. **Ranking it first in organic search is the least
valuable thing you can do**, and it is where most effort goes by default.

> **Before running anything:** confirm who controls the Google Business Profile.
> It is frequently claimed by a former employee or an agency, and recovering it
> takes weeks. Check before promising a launch date.

### Method

1. **Claim and complete the Google Business Profile.** Highest return of anything
   in this skill.
2. **Fix NAP consistency** across every listing that exists.
3. **Connect the site to the profiles** in both directions.
4. **Build a review habit**, not a review campaign.
5. **Measure enquiries by channel**, not traffic.

### Google Business Profile

For a business with a real address and local customers, this outranks the website
for the highest-intent searches. Completeness is the main lever.

| Field | Guidance |
|---|---|
| Name | **Exactly the legal/trading name.** No "Best Wedding Planner Latur" — keyword stuffing here risks suspension |
| Category | Primary: Event Planner or Wedding Planner. Secondary categories for the rest |
| Address | Exactly as on the site and every directory |
| Service area | The districts actually served |
| Hours | Real hours, including festival closures |
| Phone | The number a person answers |
| Website | The new URL, updated **on launch day** |
| Photos | The strongest work. Refresh regularly — profiles with recent photos perform better |
| Services | Each service listed with a description |
| Posts | Occasional updates; low effort, some return |
| Q&A | Seed the real questions and answer them |

**Photos on the profile are the same curation problem as the site**, and the same
consent problem — see `photo-curation` and `media-consent`. Do not upload an
album; upload the selects.

### NAP consistency

Name, Address, Phone must be **byte-identical** everywhere. Inconsistency
fragments the signals that tell a search engine these listings are one business.

```
Eventina Organisers
Kailash Plaza, Beside Manas Hotel, Barshi Road, Ganj Golai, Latur, Maharashtra 413512
+91 XXXXX XXXXX
```

Audit every place the business appears:

- Google Business Profile
- The website — footer, contact page, and `LocalBusiness` structured data
- Instagram and Facebook bios
- Justdial, Sulekha, WeddingWire, and any other directory
- Invoices and printed material, so the client stays consistent offline

**Decide the canonical form once and write it down**, including whether it is
"Barshi Road" or "Barshi Rd", and whether the phone carries a space. Give the
client the exact string.

**Everything above is `[to verify]`** until confirmed with the client — the
address here came from a directory listing, not from the business. See
`project-eventina-brand`.

### Connecting the profiles

- Website `sameAs` → Instagram, Facebook, Google Business Profile →
  `structured-data`
- Instagram bio link → the website. **Update it on launch day**; a stale link in
  the bio wastes the strongest existing channel
- Google Business Profile → the website
- Directory listings → the website
- Website → all of them, in the footer

**The Instagram-to-site link is the single highest-value connection**, because
that audience already exists. Make the landing experience good — that traffic
arrives in the Instagram in-app browser → `cross-device-testing`.

### Reviews

Google reviews influence local ranking and are the first thing a prospect reads.

- **Ask every client, at the same moment** — a few days after the event, when the
  photographs arrive. Make it a habit in the workflow, not a campaign.
- **Give a direct link** to the review form; anything requiring navigation loses
  most people.
- **Never incentivise reviews.** It violates Google's policies and risks removal
  of the reviews and the profile.
- **Reply to every review**, positive and negative. Replies are visible and a
  measured reply to a complaint reads better than no complaints at all.
- **Do not import reviews onto the site as `AggregateRating`** →
  `structured-data`. Link out instead → `testimonial-curation`.

### Directories

In this market, Justdial and Sulekha carry real traffic. Eventina already appears
on Justdial with a rating — that listing is an asset.

- **Claim the listing** if it was created by the directory rather than the
  business.
- **Correct the NAP** to the canonical form.
- **Add the website URL.**
- **Do not pay for premium placement** without measuring what the free listing
  already delivers.

### Measuring what matters

Traffic is not the metric. **Enquiries by channel** is.

- Ask "how did you hear about us?" in the enquiry form → `enquiry-conversion`
- Use a distinct UTM on the Instagram bio link
- Google Business Profile reports calls, direction requests, and website clicks
- Track calls to the `tel:` link as an event

```bash
grep -rn 'utm_source' src/ --include=*.astro           # tagged outbound links
grep -rn 'tel:' src/ --include=*.astro                  # click-to-call present
grep -rn 'how did you hear' src/ --include=*.astro -i   # attribution question
```

### Caveats

- **Google Business Profile suspensions are slow to reverse.** Keyword-stuffed
  names and fake addresses are the usual causes. Do not risk it.
- **Local ranking is heavily proximity-based**, so a business cannot rank
  everywhere. Set expectations by district.
- **This takes months.** A profile completed at launch shows results over a
  quarter, not a fortnight.
- **The client owns this work.** Reviews, posts, and photos are an ongoing habit
  the business must keep — a developer cannot do it for them. Say so, and hand
  over a written routine.
- **Everything about Eventina's address, phone, and founding date is unverified**
  until the client confirms it → `project-eventina-brand`.

### Checklist

- [ ] Google Business Profile ownership confirmed with the client
- [ ] Profile complete: categories, hours, services, photos, Q&A
- [ ] Business name exactly as trading, no keywords
- [ ] Canonical NAP string decided and written down
- [ ] NAP identical across site, structured data, social, and directories
- [ ] Website URL updated on the profile on launch day
- [ ] Instagram bio link updated with a UTM
- [ ] Justdial and Sulekha listings claimed and corrected
- [ ] Review-request habit built into the client's post-event workflow
- [ ] Direct review link prepared
- [ ] No incentivised reviews
- [ ] Reply routine agreed for reviews
- [ ] "How did you hear about us?" on the enquiry form
- [ ] Click-to-call tracked
- [ ] Written ongoing routine handed to the client

## References

- **Google Business Profile Help — represent your business, guidelines for
  business names, and prohibited content**
  <https://support.google.com/business/answer/3038177>
- **Google Business Profile Help — prohibited and restricted content, including
  incentivised reviews** <https://support.google.com/business/answer/7400114>
- **Google Search Central — local business structured data**, the on-site half of
  NAP consistency
  <https://developers.google.com/search/docs/appearance/structured-data/local-business>
- **Justdial listing — Eventina Organisers, Ganj Golai, Latur**, the source of
  the address, founding year, and rating recorded as `[to verify]`
  <https://www.justdial.com/Latur/Eventina-Organisers-Near-Manas-Hotel-Latur-HO/9999P2382-2382-191109130517-W8Y2_BZDET>

**Not sourced — written for this framework:** the ranked discovery-channel list
for this business, the "ranking first in organic is the least valuable thing"
position, the canonical-NAP-string practice, the review-habit-not-campaign
framing, and the enquiries-by-channel measurement approach.
