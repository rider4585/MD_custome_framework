---
name: image-rights-and-credit
version: 1.0.0
description: |
  Protect and attribute a photographer's work on the web — copyright ownership,
  embedded IPTC metadata, watermarking decisions, Google's licensable-image
  markup, second-shooter credit, and what to do when work is stolen. Use before
  publishing any image, when the client asks about watermarks or theft, or when
  crediting a second shooter or film team.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## Image Rights and Credit

A photographer's entire asset is a set of files that are trivially copyable, and
a portfolio's whole purpose is to put them in public. That tension cannot be
removed, only managed — and it is managed with **metadata and attribution**, not
with technical prevention.

The framing that matters: **you cannot stop copying, so optimise for
traceability and credit instead.** Every measure below is judged by whether it
survives a screenshot, a repost, and a reupload.

> **Before running anything:** load `project-client-brand` for who actually
> owns the copyright in this studio's work. Ownership between a studio, a
> second shooter, and a couple is contractual and cannot be assumed.

### Method

1. **Establish ownership in writing** — studio, second shooter, film team, couple.
2. **Embed IPTC and XMP metadata** into every published and delivered file.
3. **Decide the watermark policy** per surface, using the table below.
4. **Emit licensing structured data** so Google can show the licence.
5. **Credit collaborators** by name, on the page.
6. **Set up detection and a response ladder** for theft.

### Ownership — get this right first

| Situation | Default position | What must be in writing |
|---|---|---|
| Studio principal shoots | Studio owns copyright | Nothing extra |
| **Second shooter / associate** | **The photographer who pressed the shutter owns copyright unless assigned** | An assignment or licence in the engagement terms. This is the most common gap |
| Film team subcontracted | Same as second shooter | Assignment, plus music licence chain → `film-showcase` |
| Couple / client | Buys a **licence to use**, not copyright, unless assigned | The contract must say which |
| Venue or planner reposting | No rights at all without a grant | A written, scoped permission → `vendor-network` |

**"They worked for me that day" does not transfer copyright** in most
jurisdictions absent an employment relationship or a written assignment. If the
studio cannot produce assignments for its second shooters, that is a finding to
report — publishing those frames is a risk the studio may not know it is taking.

Copyright ownership is separate from the **consent** of the people depicted.
Owning the photograph does not grant the right to publish someone's face for
marketing. Both are required → `media-consent`.

### Embedded metadata

Metadata travels with the file. It is the only attribution that survives a
download and a re-upload, and Google reads it.

```bash
# Embed IPTC/XMP credit on every published derivative — run in the build pipeline
exiftool -overwrite_original \
  -IPTC:By-line="Studio Name" \
  -IPTC:CopyrightNotice="© 2026 Studio Name. All rights reserved." \
  -IPTC:Credit="Studio Name" \
  -XMP-dc:Creator="Studio Name" \
  -XMP-dc:Rights="© 2026 Studio Name" \
  -XMP-plus:ImageCreatorName="Studio Name" \
  -XMP-xmpRights:WebStatement="https://studio.example/licensing/" \
  -XMP-xmpRights:UsageTerms="Licensed for personal use by the client. Commercial use requires written permission." \
  public/images/work/**/*.jpg

# Verify — this is the check that belongs in CI
exiftool -IPTC:CopyrightNotice -XMP-xmpRights:WebStatement -s3 public/images/work/*.jpg \
  | grep -c '^$' && echo "WARNING: images missing rights metadata"
```

**Most image pipelines strip metadata by default** as a size optimisation,
including Astro's built-in Sharp integration. This is the single most common way
a studio's attribution silently disappears. Check the built output, not the
source:

```bash
exiftool -IPTC:By-line dist/_astro/*.jpg | head
```

If it is stripped, either configure the pipeline to preserve it or re-embed after
build, before deploy → `image-optimization`, `asset-workflow`, `static-deploy`.

The cost is roughly 2–8 KB per image. Pay it.

### Watermarking — per surface

| Surface | Watermark? | Reasoning |
|---|---|---|
| **Portfolio / case studies** | **No** | A watermark on the work you are selling damages the work. This is the page where the photograph must be perfect |
| **Client gallery previews** | Light, corner, small | Discourages screenshot-sharing of proofs before delivery → `client-gallery-delivery` |
| **Client full-resolution downloads** | Never | They paid for clean files |
| **Family sharing links** | Optional, light | A judgement call |
| **Social posts** | Studio's choice | A visible handle in-frame travels further than a metadata field |

**The argument the client will make is "a watermark stops theft".** It does not —
it is cropped or cloned out in seconds — and its guaranteed effect is that every
prospective couple sees the studio's best work with a logo across it. Trade a
certain cost against an uncertain benefit; recommend no watermark on the
portfolio. Then defer, because it is their work.

**Right-click disabling and transparent overlay `<div>`s are worse than
useless.** They stop nobody, break legitimate use, and interfere with assistive
technology → `accessibility`. Do not ship them.

Resolution is the real lever: publish at the size the layout needs and no larger.
A 2000px-wide file serves every display without being a print-ready file →
`image-optimization`.

### Licensable-image structured data

Google supports a licensing badge and a licence link in Google Images. It is one
of the few structured-data types that produces a visible, useful result for a
photographer.

```json
{
  "@context": "https://schema.org",
  "@type": "ImageObject",
  "contentUrl": "https://studio.example/images/work/sharma-01.jpg",
  "creator":       { "@type": "Organization", "name": "Studio Name" },
  "creditText":    "Studio Name",
  "copyrightNotice": "© 2026 Studio Name",
  "license":       "https://studio.example/licensing/",
  "acquireLicensePage": "https://studio.example/contact/"
}
```

- **`license`** points at a page describing the terms; **`acquireLicensePage`**
  at where someone can ask for permission. Both are needed for the badge.
- **The licensing page must actually exist** and state real terms. Marking up a
  page that does not describe a licence is a guidelines violation →
  `structured-data`.
- **Generate it from the content collection**, per image, so it cannot drift →
  `content-collections`.
- The `creditText` and `copyrightNotice` here should match the embedded IPTC
  fields exactly. Mismatched values are worse than one source.

### Crediting collaborators

Credit is the currency of this industry, and failing to give it is how a studio
loses its network → `vendor-network`.

- **Name the second shooter** on any case study containing their frames, on the
  page, not only in metadata.
- **Name the film team**, and vice versa — a film page should credit the stills
  photographer.
- **Credit the vendors the couple hired** — planner, decor, makeup, venue —
  where the studio knows them. It costs nothing and it is why planners refer.
- **Link out** where a collaborator has a site. `rel="nofollow"` is unnecessary
  for genuine editorial credit → `seo-foundations`.
- **Ask how each person wants to be credited.** Some want a business name, some a
  personal one, some a handle.

Store it in frontmatter so it renders consistently:

```yaml
credits:
  photography: "Studio Name"
  second_shooter: { name: "Name", url: "https://…" }
  film: { name: "Studio Films", url: "https://…" }
  vendors:
    - { role: "Decor",  name: "…", url: "…" }
    - { role: "Makeup", name: "…", url: "…" }
```

### When work is stolen

A response ladder, cheapest rung first:

1. **Detect.** Google reverse image search on your top 20 frames, quarterly. Set
   a calendar reminder; nobody does this without one.
2. **Assess.** A vendor reposting with credit is a networking opportunity, not
   theft. A competitor using your frames in *their* portfolio is the serious
   case — it is a misrepresentation of authorship, not just a copy.
3. **Ask.** A short, non-hostile message asking for credit or removal resolves
   most cases, and preserves a relationship you may want.
4. **Escalate to the platform.** Instagram and Facebook have copyright report
   forms; hosts have DMCA agents. Your embedded IPTC metadata and the original
   RAW with its EXIF capture date are the evidence — this is why step 2 of the
   Method exists.
5. **Legal.** Proportionate only for commercial misuse at scale. The studio's
   call, with their own advisor.

**Keep the RAWs.** An original RAW with its camera serial and capture timestamp
is the strongest available proof of authorship, and it is worth stating a
retention period for exactly this reason → `asset-workflow`.

### Caveats

- **This is not legal advice.** Copyright, moral rights, and personality rights
  differ by jurisdiction; India's Copyright Act, and the treatment of
  commissioned photographs in particular, has provisions worth a lawyer's
  reading before the studio relies on any default stated here.
- **Ownership defaults vary.** The "shutter-presser owns it" rule holds broadly
  but not universally, and employment and commission relationships change it.
- **Metadata can be stripped by anyone downstream**, including social platforms,
  most of which remove IPTC on upload. It is evidence, not protection.
- **The licensable badge is not guaranteed to appear** — like all structured
  data, it makes a result eligible, not certain.
- **`exiftool` must be installed** in the build environment for the CI check to
  run; a silently skipped check is worse than none.

### Checklist

- [ ] Copyright ownership established in writing for the studio, second shooters,
      and film team
- [ ] Assignment gaps reported, not published around
- [ ] IPTC and XMP rights fields embedded in every published derivative
- [ ] Metadata verified in the **built** output, not just the source
- [ ] Rights metadata check running in CI
- [ ] Watermark policy decided per surface; portfolio unwatermarked
- [ ] No right-click blocking or overlay tricks
- [ ] Published resolution no larger than the layout needs
- [ ] `ImageObject` with `license` and `acquireLicensePage` generated per image
- [ ] A real licensing page exists and states real terms
- [ ] Metadata and structured-data values match exactly
- [ ] Second shooters, film team, and vendors credited on the page, as they asked
- [ ] Quarterly reverse-image check scheduled
- [ ] RAW retention period stated

## References

- **IPTC Photo Metadata Standard 2024.1** — `By-line`, `CopyrightNotice`,
  `Credit`, and the XMP rights properties used above
  <https://www.iptc.org/std/photometadata/specification/IPTC-PhotoMetadata>
- **Google Search Central — Image licence structured data**, the requirements for
  the licensable badge in Google Images
  <https://developers.google.com/search/docs/appearance/structured-data/image-license-metadata>
- **Google Search Central — Image SEO best practices**, on Google reading
  embedded IPTC rights fields
  <https://developers.google.com/search/docs/appearance/google-images>
- **ExifTool documentation** — the IPTC and XMP tag names used in the commands
  <https://exiftool.org/TagNames/IPTC.html>
- **CIPA / IPTC "Embedded Metadata Manifesto"**, on preserving metadata through
  processing pipelines <https://www.embeddedmetadata.org/>
- **This framework's `media-consent`** — the separate, DPDP-based permission of
  the people depicted, which copyright ownership does not supply

**Not sourced — written for this framework:** the traceability-over-prevention
framing, the ownership table, the per-surface watermark policy and the argument
against portfolio watermarks, the metadata-must-match-structured-data rule, the
credit frontmatter shape, and the five-rung theft response ladder.
