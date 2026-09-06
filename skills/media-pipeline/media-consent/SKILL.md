---
name: media-consent
version: 1.0.0
description: |
  Establish and record permission to publish photographs and video of identifiable
  people — guests, children, clients, staff — and build the erasure path. Use
  before publishing any event media, when setting up the content model, or when
  someone asks to be removed.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
---

## Media Consent

An event portfolio publishes photographs of people who did not hire the
photographer and may not know the pictures exist. Guests, children, staff, and
vendors all appear. **Consent to be photographed at a wedding is not consent to
appear on a company's marketing website** — different purposes, and the second
one needs asking.

In India this became an engineering requirement rather than an etiquette
question when the **DPDP Rules, 2025 were notified on 14 November 2025**,
operationalising the Digital Personal Data Protection Act, 2023 with an
eighteen-month phased compliance window.

> **⚠️ This skill is not legal advice.** It exists so an agent raises the
> question, records the client's decision, and builds the technical path for
> erasure. Anything with legal consequence goes to the client and their own
> advisor, with a named human decision. Never let an agent decide what the law
> permits.

### Method

1. **Get consent captured at booking**, in the client contract — retrofitting it
   after the event is much harder.
2. **Classify each image** by who is identifiable in it.
3. **Record consent as data**, in the content model, not in an email thread.
4. **Block publication at build time** where consent is absent.
5. **Build and test the erasure path** before launch, not after the first request.

### Who needs consent

| Subject | Requirement |
|---|---|
| **The client** (couple, company) | Contract clause covering marketing use. Usually the easiest |
| **Identifiable guests** | Consent, or the image is not published. Crowd shots where no individual is the subject are a lower-risk grey area — **ask the client's advisor, do not decide** |
| **Children** | **Verifiable guardian consent.** The DPDP framework treats children's data with heightened obligations. This framework's position: if in doubt, do not publish |
| **Staff and vendors** | Consent, especially where named |
| **Testimonial subjects** | Separate consent for quote, name, and photograph — see `testimonial-curation` |

**Default to "do not publish" when consent is unclear.** An unpublished
photograph costs one image. A published one that must be withdrawn costs
credibility, and potentially more.

### The booking clause

The cheapest intervention by an order of magnitude. Suggest the client add a
marketing-use clause to their booking contract, with:

- **Specific purpose** — "publication on the company website and social media
  accounts", not "marketing purposes"
- **Scope** — which functions, which people
- **Duration and withdrawal** — how to withdraw and how quickly it is honoured
- **Guest handling** — who tells guests, and how

Under the DPDP framework the notice must be clear, specific about purpose, and
consent must be freely given, informed, and withdrawable. A buried blanket clause
is unlikely to satisfy that. **Get the clause drafted by the client's advisor.**

### Recording consent as data

Consent that lives in a WhatsApp thread cannot be audited or acted on. Put it in
the content model so the build can enforce it.

```yaml
# src/content/events/sharma-wedding-2026.md
title: "The Sharma Wedding"
mediaConsent:
  clientContract: "contracts/2026-01-sharma.pdf#clause-7"
  grantedBy: "Anjali Sharma"
  grantedOn: 2026-01-12
  scope: "website and Instagram"
  childrenPresent: true
  childrenConsent: "guardian, written, 2026-02-16"
  withdrawalContact: "anjali.sharma@example.com"
  verified: true
gallery:
  - src: ./01-mandap.jpg
    identifiable: ["client"]
    consent: true
  - src: ./07-guests-dancing.jpg
    identifiable: ["guests"]
    consent: true
    note: "Verbal consent from all four foreground subjects, recorded 2026-02-16"
  - src: ./11-children-stage.jpg
    identifiable: ["children"]
    consent: false          # excluded from the build
```

```ts
// Enforced in the collection schema — see `case-study-structure`
mediaConsent: z.object({
  clientContract: z.string(),
  grantedOn:      z.coerce.date(),
  scope:          z.string(),
  verified:       z.literal(true),   // build fails if not literally true
}),
gallery: z.array(z.object({
  src:          image(),
  identifiable: z.array(z.enum(['client','guests','children','staff','vendors','none'])),
  consent:      z.boolean(),
})).refine(
  imgs => imgs.every(i => i.consent || i.identifiable.includes('none')),
  { message: 'Every image with identifiable people needs consent: true' }
),
```

**The build failing is the point.** A consent field that can be left blank will
be left blank.

### The erasure path

Someone will ask to be removed, and the DPDP framework gives data principals
the right to erasure. Have this working before launch:

1. **A findable contact route** — a named email on the privacy page, not a form.
2. **A locate step** — grep the content directory by person or event.
3. **A removal that is one edit** — set `consent: false`, or delete the entry;
   the build drops the image.
4. **Purge the CDN**, or the image stays live at its URL after the page changes.
   **This is the step that gets missed.** A static-site image is a plain URL and
   survives the page that referenced it.
5. **Remove the source file** from the public output, not just the reference.
6. **Log it** — what, when, requested by whom, confirmed to whom.
7. **Social media too.** Removal from the site is not removal from Instagram.

```bash
# Locate every reference before removing
grep -rn "07-guests-dancing" src/content/ src/pages/
find dist public -name '*07-guests-dancing*'
```

**Target: complete within a week.** Test the whole path with a dummy request
before launch.

### Adjacent technical duties

- **Strip EXIF before publishing.** Event photographs carry GPS coordinates of
  a private home or venue. See `asset-workflow`.
- **Publish a privacy notice** stating what is collected via the enquiry form,
  the purpose, retention, and the contact for erasure.
- **Prefer cookieless analytics** (Umami, Plausible) — no consent banner needed,
  and less personal data to account for. See `static-deploy`.
- **Do not index removed images.** After erasure, confirm the URL 404s.

### Caveats

- **Neither this framework nor an agent may decide what the law requires.** These
  are engineering and record-keeping practices to support a decision the client
  and their advisor make.
- **The eighteen-month phased compliance window from November 2025 is not a
  reason to defer the technical work.** Building the erasure path later means
  rebuilding the content model.
- **Cultural expectations differ from legal ones.** A family may be entirely
  comfortable with photographs but not with their name and city together. Ask
  about both.
- **Consent can be withdrawn.** Design for it as a normal operation, not an
  incident.

### Checklist

- [ ] Marketing-use clause in the booking contract, drafted by the client's advisor
- [ ] Every image classified by who is identifiable
- [ ] Consent recorded as structured data, not in a message thread
- [ ] Children's images excluded unless verifiable guardian consent exists
- [ ] Build fails when consent is missing
- [ ] Erasure contact published and monitored
- [ ] Erasure path tested end to end, including CDN purge and 404 verification
- [ ] EXIF stripped from every published image
- [ ] Privacy notice published
- [ ] Cookieless analytics chosen, or a consent banner implemented properly
- [ ] Client informed in writing that this is not legal advice

## References

- **Digital Personal Data Protection Act, 2023** — MeitY, the governing statute
  <https://www.meity.gov.in/data-protection-framework>
- **DPDP Rules, 2025 — PIB press note (notified 14 November 2025)**, on the
  separate consent notice, purpose specificity, and the eighteen-month phased
  compliance window
  <https://www.pib.gov.in/PressNoteDetails.aspx?NoteId=156054&ModuleId=3&reg=3&lang=2>
- **Astro docs — content collection schemas and Zod `refine()`**, the build-time
  enforcement mechanism used above
  <https://docs.astro.build/en/guides/content-collections/>
- **ExifTool** — metadata inspection and removal
  <https://exiftool.org/>
- **`testimonial-curation`, `asset-workflow`, `case-study-structure`** (this
  framework) — the adjacent workflows this skill gates

**Not sourced — written for this framework:** the subject classification table,
the consent data model, the build-fails-on-missing-consent approach, the
seven-step erasure path including the CDN-purge trap, and the one-week target.
Legal interpretation is explicitly out of scope.
