---
name: client-gallery-delivery
version: 1.0.0
description: |
  Deliver finished photographs and films to the couple — private galleries,
  access control, downloads, expiry, and the hard rule that a delivery surface
  is not a marketing surface. Use when planning delivery, when a client asks for
  a "client login", or when private galleries risk leaking into search results.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## Client Gallery Delivery

Delivery is where a photography business is judged after the money has changed
hands. It is also where a static marketing site meets its limit: a portfolio can
be entirely static, and a client gallery fundamentally cannot, because it needs
authentication, per-client data, and full-resolution downloads.

**The central rule: the marketing site and the delivery system are two systems.**
Merging them is the mistake this skill exists to prevent. A private gallery
bolted into the public site leaks — into the sitemap, into search results, into
an OG preview — and a leaked wedding gallery is a serious breach of a couple's
trust and, under India's DPDP framework, of their rights → `media-consent`.

> **Before running anything:** load `media-consent`. Consent to *receive* photos
> and consent to *publish* them are different permissions, and delivery must not
> be treated as evidence of the second.

### Method

1. **Decide build vs. buy** using the table below. For almost every studio, buy.
2. **Separate the surfaces** — different subdomain, different auth, never in the
   marketing site's route tree.
3. **Set retention and expiry deliberately**, and tell the couple in writing.
4. **Exclude every delivery URL from indexing** and verify it after launch.
5. **Define the promotion path** — how a delivered frame becomes a portfolio
   frame, with consent, as a deliberate act.

### Build or buy

| | Buy (Pic-Time, Pixieset, ShootProof, SmugMug…) | Build |
|---|---|---|
| Auth, downloads, expiry | Included | You are building an auth system |
| Full-resolution storage and egress | Included in the plan | Metered, and a wedding is 20–60 GB |
| Client-side ordering / prints | Included, revenue-generating | Months of work |
| Branding | Limited to a logo and colours | Total |
| Cost | A monthly subscription | Engineering time, then hosting, then maintenance forever |
| Data location | The provider's, worth checking | Yours |

**Recommendation: buy, and link to it from the site.** Building a delivery
platform is not a portfolio project — it is a product, and the studio's money is
better spent on the portfolio that wins the booking. Build only when there is a
requirement no provider meets, and name that requirement before starting.

The site's job is then small and worth doing well: a `/clients/` page with a
single clear entry point, the studio's own type and colour around it, and an
honest note about what the client will find inside.

### Separating the surfaces

```
studio.example              ← marketing. Static, public, indexed
gallery.studio.example      ← delivery. Authenticated, noindex, never linked publicly
```

Non-negotiables, whichever route is chosen:

- **A separate host or subdomain.** Route-level separation inside one static site
  is one misconfigured redirect away from exposure.
- **`X-Robots-Tag: noindex, nofollow, noimageindex` on every delivery response**,
  set at the header level. A `<meta>` tag does not cover the images themselves.
- **Delivery URLs never appear in the sitemap, the RSS feed, or any OG tag.**
- **No public search.** Galleries are found by link, not by browsing.
- **Unguessable URLs are not access control.** A long token in a URL is a
  convenience; a password or an account is the control. Use both.
- **Watermark previews, never the full-resolution download** →
  `image-rights-and-credit`.

```bash
# Verify after launch — all three must come back clean
curl -sI https://gallery.studio.example/g/sample | grep -i 'x-robots-tag'
curl -s  https://studio.example/sitemap-index.xml | grep -i 'gallery\.' && echo "LEAK: gallery URL in sitemap"
grep -rn 'gallery\.studio\.example' src/ --include=*.astro --include=*.md
```

Add the sitemap check to CI, not to a launch checklist someone runs once →
`launch-review`.

### Expiry and retention

The most common client complaint in this industry is a gallery that expired
before the couple downloaded it, usually while they were on honeymoon.

| Setting | Recommendation | Why |
|---|---|---|
| **Download window** | 12 months minimum, stated at booking | Anything shorter generates support work and bad reviews |
| **Gallery visibility** | 12–24 months, then archived on request | Storage cost is real |
| **Studio archive of masters** | State a definite period — and that it is not a backup service | Couples assume "forever". They are wrong, and they should be told so before they need to know |
| **Expiry warning** | Two emails: 30 days and 7 days before | Removes almost all of the complaint |
| **Extension** | Possible, and priced or free — decide and publish it | An unanswered question becomes a dispute |

**Put the retention policy on the site in plain language**, not only in the
contract. It is a trust signal, and it prevents the conversation where a couple
asks for their wedding photos five years later and hears no →
`booking-and-packages`.

**Deletion must actually delete**, including CDN copies and provider backups.
`media-consent` documents the seven-step erasure path; delivery is the surface
where an erasure request most often arrives.

### Downloads

- **Offer two sizes**: full-resolution for print, and web-sized for sharing.
  Couples who are given only full-resolution files upload 8 MB JPEGs to Instagram
  and get compressed to mush, which reflects on the photographer.
- **Embed IPTC credit and copyright in every downloaded file** — it survives
  re-sharing and is the studio's only attribution once a file is loose →
  `image-rights-and-credit`.
- **A single ZIP of everything** is what people actually want. Provide it even
  when the provider's default is per-image.
- **Never require an app** to download.
- **Sharing links that couples forward to family** should be a distinct, lower-
  privilege link — viewable, not downloadable at full resolution.

### From delivery to portfolio

A delivered gallery is not a licence to publish. The promotion path:

1. **Check consent.** Written, specific to marketing use, still valid, and
   covering every identifiable person in the frame → `media-consent`.
2. **Check the refusal list.** Couples often approve the wedding but exclude
   getting-ready frames, a specific relative, or children. Honour it exactly.
3. **Cull to the signature** → `signature-style`, `photo-curation`.
4. **Re-grade for the web set**, because a delivery grade and a portfolio grade
   are not the same → `color-mood`.
5. **Move it into the content collection** as a deliberate commit, with the
   consent reference in frontmatter → `content-collections`, `asset-workflow`.

**Never automate step 1.** A build that can promote images without a human
confirming consent will eventually publish something it should not.

### Caveats

- **Provider feature sets and pricing change frequently.** The build/buy table is
  a shape, not a current comparison — check today's plans before recommending one.
- **Data residency may matter** for Indian clients depending on the provider and
  the studio's contractual commitments. Ask where galleries are stored.
- **Password-protected does not mean encrypted.** Assume the provider can read
  the galleries, and say so if a client asks.
- **This skill does not cover payments, contracts, or print fulfilment**, which
  most providers also handle and which have their own compliance surface.
- **The retention numbers above are this framework's recommendations**, not an
  industry standard.

### Checklist

- [ ] Build-vs-buy decided, with the requirement named if building
- [ ] Delivery on a separate host or subdomain, never in the marketing route tree
- [ ] `X-Robots-Tag: noindex, nofollow, noimageindex` verified on a live response
- [ ] No delivery URL in the sitemap, feeds, or OG tags — checked in CI
- [ ] Password or account required; URL entropy not relied on as access control
- [ ] Previews watermarked; full-resolution behind auth
- [ ] Download window, gallery lifetime, and archive period decided and published
- [ ] Expiry warnings at 30 and 7 days
- [ ] Full-resolution and web-sized downloads both offered, plus a full ZIP
- [ ] IPTC credit and copyright embedded in downloads
- [ ] Family sharing links are view-only
- [ ] Erasure path works end to end, including CDN and provider backups
- [ ] Promotion to the portfolio gated on a human consent check

## References

- **This framework's `media-consent`** — the DPDP-based consent model and the
  seven-step erasure path this skill defers to
- **Google Search Central — Block search indexing with `noindex`**, on
  `X-Robots-Tag` covering non-HTML resources such as images
  <https://developers.google.com/search/docs/crawling-indexing/block-indexing>
- **Google Search Central — `noimageindex`**
  <https://developers.google.com/search/docs/crawling-indexing/robots-meta-tag>
- **IPTC Photo Metadata Standard** — the `Creator`, `CopyrightNotice`, and
  `CreditLine` fields embedded in delivered files
  <https://www.iptc.org/standards/photo-metadata/>
- **OWASP — Authentication Cheat Sheet**, for the build route
  <https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html>

**Not sourced — written for this framework:** the two-systems rule, the
build-vs-buy recommendation, the retention table and its numbers, the two-tier
download and view-only-sharing model, and the five-step promotion path with its
no-automation rule.
