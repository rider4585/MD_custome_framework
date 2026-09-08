---
name: testimonial-curation
version: 1.0.0
description: |
  Source, verify, edit, and present client testimonials and reviews so they read
  as real. Use when adding social proof to any page, when testimonials all sound
  the same, or when deciding how to surface Google and Justdial ratings.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
---

## Testimonial Curation

Testimonials are the highest-trust content on a service site and the easiest to
fake, which is why visitors discount them heavily. **Everything in this skill is
about restoring the credibility that the format has lost.**

A generic five-star quote with a first name and no photograph is worth roughly
nothing. A specific, slightly awkward quote with a full name, the event, and the
date is worth a great deal.

### Method

1. **Ask at the right moment** — within a week of the event, while it is vivid.
2. **Ask a question that cannot be answered generically.**
3. **Record explicit consent** for the quote, the name, and any photograph, as
   three separate permissions.
4. **Edit only for length and clarity.** Never for voice.
5. **Attribute as fully as consent allows**, and place near the relevant proof.

### Asking

Do not ask "how did we do?" — it produces "excellent, very professional".

Ask instead:

- "What were you most worried about before the day, and what happened?"
- "What did we do that you did not expect?"
- "What would you tell a friend who is deciding?"

The first question is the strongest, because the answer contains a problem — the
same reason `case-study-structure` requires one.

### What makes a testimonial credible

| Credible | Not credible |
|---|---|
| Names a specific worry and its resolution | "Everything was perfect" |
| Uses the client's own phrasing, including imperfect grammar | Agency-smooth prose |
| Full name, or first name + surname initial | "S., happy customer" |
| Event type and month/year | No date |
| Photograph of the client, with consent | Stock photograph — never |
| Sits next to the work it describes | A rotating carousel of quotes |

**Never use a stock photograph next to a testimonial.** It is a fabricated
record, it is trivially reverse-searchable, and discovery destroys the credibility
of everything else on the site. If there is no photograph, use no photograph.

### Editing

Permitted: trimming length, removing filler, fixing a typo, cutting a digression.

**Not permitted:** improving the grammar into agency voice, merging two clients'
words, changing the claim, or adding specificity that was not there. Awkward
phrasing is a credibility asset.

If a quote is trimmed, keep it faithful to the original meaning. If you must show
that material was cut, use an ellipsis.

### Consent — three separate permissions

Under India's DPDP framework, a testimonial with a name and photograph is
personal data being processed for a stated purpose. See `media-consent` for the
full workflow; the minimum here:

```yaml
testimonial:
  quote: "..."
  author: "Sonal Deshmukh"
  event: "Wedding, Latur"
  date: 2026-02-14
  consent:
    quote: true          # to publish the words
    name: true           # to attribute by name
    photo: false         # to use their photograph — separately granted
    grantedOn: 2026-02-20
    grantedVia: "WhatsApp message, screenshot in /consent/2026-02-20-deshmukh.png"
    withdrawable: true
```

**Consent to be photographed at the event is not consent to appear on the
website.** These are different purposes, and treating them as one is the most
common mistake in this category.

### Third-party ratings

the studio holds public ratings on Justdial and similar directories. These are
stronger proof than site-hosted quotes because the visitor knows the company did
not write them.

- **Link out to the source.** An unlinked "4.8/5" is a claim; a linked one is
  evidence.
- **State the count and the date read.** "4.8 from 93 reviews on Justdial, read
  September 2026."
- **Do not mark up third-party ratings as your own `AggregateRating`** in
  structured data. Google's guidelines restrict self-serving review markup, and
  aggregating someone else's ratings into your own schema risks a manual action.
  See `structured-data`.
- **Google Business Profile reviews are the highest-value target** for this
  business — see `local-discovery`.

### Placement

- **Next to the proof, not in a carousel.** A quote about a venue change belongs
  in that case study, immediately after the problem section.
- **One per page section**, not a wall.
- **Never auto-rotate.** Auto-advancing carousels are ignored, and they fail WCAG
  SC 2.2.2 unless pausable.

### Caveats

- **A thin testimonial set is better shown thin.** Three real ones beat twelve
  padded.
- **Withdrawal must be honoured.** If a client asks for removal, remove it — and
  the DPDP framework gives them that right. Build the content model so removal is
  a one-line change; see `case-study-structure`.
- **Never write a testimonial for a client to approve.** Even with approval it is
  a fabricated record, and it always reads like one.
- **This is not legal advice.** Consent language should be reviewed by the client's
  own advisor.

### Checklist

- [ ] Asked within a week of the event, with a non-generic question
- [ ] Quote contains a specific worry or surprise, not a general endorsement
- [ ] Client's own phrasing preserved; edits limited to length and clarity
- [ ] Full attribution to the extent consent allows
- [ ] Quote, name, and photo consents recorded separately with date and evidence
- [ ] No stock photography anywhere near a testimonial
- [ ] Third-party ratings linked to source, with count and read date
- [ ] No self-serving `AggregateRating` markup for third-party reviews
- [ ] Placed beside the work described; no auto-rotating carousel
- [ ] Removal path is a one-line content change

## References

- **Digital Personal Data Protection Act, 2023 and DPDP Rules, 2025 (notified
  14 November 2025)** — purpose limitation, informed consent, and the right to
  erasure, which together govern named testimonials and photographs
  <https://www.pib.gov.in/PressNoteDetails.aspx?NoteId=156054&ModuleId=3&reg=3&lang=2>
- **Google Search Central — Review snippet structured data guidelines**, on
  self-serving reviews and third-party rating markup
  <https://developers.google.com/search/docs/appearance/structured-data/review-snippet>
- **Nielsen Norman Group — trustworthiness and credibility signals in web design**
  <https://www.nngroup.com/articles/trustworthy-design/>
- **WCAG 2.2 — SC 2.2.2 Pause, Stop, Hide**, which auto-rotating testimonial
  carousels routinely fail <https://www.w3.org/TR/WCAG22/#pause-stop-hide>

**Not sourced — written for this framework:** the three questions to ask, the
credible/not-credible table, the three-separate-permissions model, the placement
rules, and the position on stock photography.
