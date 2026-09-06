---
name: photo-curation
version: 1.0.0
description: |
  Cull four hundred event photographs down to the twelve that belong on the site,
  and grade them into one consistent look. Use before building any gallery or
  case study, when the archive is inconsistent, or when deciding whether a new
  shoot is needed.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## Photo Curation

The hardest and highest-leverage work on a portfolio site is **deciding what not
to show**. A client will send an album of four hundred images and every one of
them matters to them. Roughly twelve belong on the site.

**This skill runs before design.** Selection determines layout, not the reverse.

> **Before running anything:** load `emotional-brief` for the register and
> `art-direction` for the six image roles. You are culling *to fill roles*, not
> picking favourites.

### Method

1. **Pass 1 — technical reject.** Fast, mechanical, no taste involved.
2. **Pass 2 — role fill.** Find the best candidate for each role.
3. **Pass 3 — grade check.** Can these live together?
4. **Pass 4 — sequence.** Order them; cut what does not earn its place.
5. **Pass 5 — consent check.** Every recognisable face cleared.

### Pass 1 — technical reject

Cut without discussion:

- Out of focus on the subject (motion blur *in the background* is fine and often
  good)
- Blown highlights on skin, or blocked-up shadows with no detail
- Harsh direct flash producing a hard shadow behind the subject
- Mid-blink, mid-sentence, mid-chew
- Cluttered background — exit signs, cables, catering trays, a stray phone
- Under about 2000 px on the long edge — cannot serve a retina hero
- Heavy in-camera filters or someone else's watermark

This typically removes 60–70% of an album and it costs almost no judgement.

### Pass 2 — role fill

For each role from `art-direction` — establishing, hero, detail, human, scale,
closing — pick the best two candidates, then choose one.

**The hero is chosen last**, after the others, because it must be the emotional
peak *relative to what surrounds it*.

Rules that repeatedly matter:

- **Faces reacting beat faces posing.** A guest watching is worth three group
  photographs.
- **Hands, texture, and detail prove craft** better than a wide shot of a
  decorated room.
- **One wide crowd shot is enough.** Two says you had nothing else.
- **Avoid the photographer's own showpiece** — the dramatic backlit couple shot —
  unless it fits the register. It is usually about the photographer.

### Pass 3 — grade consistency

**This is the pass most often skipped and it is the one that decides whether the
site looks professional.** Event albums are frequently shot by several
photographers, across a day and a night, and graded differently.

Check across the selected set:

| Axis | What to match |
|---|---|
| White balance | Skin tones consistent; no set drifting green or magenta |
| Exposure | Comparable mid-tone brightness |
| Contrast | One contrast character, not some flat and some crushed |
| Saturation | Especially reds — Indian wedding clothing clips red easily |
| Black point | All lifted, or none |

If two images cannot be reconciled, **cut one**. A grid that shifts colour
between tiles reads as amateur no matter how good the individual frames are.

```bash
# Quick sanity scan of the selected set (ImageMagick)
for f in selects/*.jpg; do
  printf '%-40s %s\n' "$(basename "$f")" \
    "$(magick "$f" -resize 1x1 -format '%[pixel:p{0,0}]' info:)"
done
```

That prints each image's average colour — an outlier is usually a grade
mismatch. It is a smell test, not a verdict; look at the images.

### Pass 4 — sequence and cut

Lay the selects out in order per `art-direction`'s size-alternation rhythm. Then
ask of each image: **what does this one do that its neighbours do not?** No
answer means cut.

Getting from twenty to fourteen is where the quality is.

### Pass 5 — consent

Every recognisable person needs consent before publishing. Children need
guardian consent. See `media-consent` — this is a blocking gate, not a
formality, and under the DPDP framework it has legal weight.

### When the archive is not good enough

Say so, early, in writing. Signals:

- Fewer than nine usable frames for a flagship event
- No detail shots at all — common when only a video team attended
- Everything shot from the back of the room
- Only posed group photographs

**A shoot is cheaper than a portfolio that undersells the company.** Present the
evidence — a contact sheet of what survived Pass 1 — rather than an opinion. If
a reshoot is impossible, reduce the number of published case studies rather than
padding with weak frames; see `brand-narrative` on selection as a premium signal.

### Caveats

- **You are curating someone's memories.** The couple's favourite photograph may
  fail Pass 1. Explain in terms of the site's job, not the photograph's quality,
  and expect to lose some of these arguments.
- **Do not alter reality.** Grading for consistency is fine; removing a person,
  changing skin tone, or compositing is not — it is a portfolio, i.e. a factual
  claim about work performed.
- **Culling is not a solo activity.** A second pair of eyes catches the frame you
  fell in love with for reasons unrelated to the site.
- **Keep the rejects.** Requirements change; a Pass 1 reject may be the only
  establishing shot available later.

### Checklist

- [ ] Pass 1 technical reject completed mechanically
- [ ] Every image role from `art-direction` filled
- [ ] Hero chosen last, relative to its neighbours
- [ ] Grade consistency verified across the whole selected set
- [ ] Irreconcilable images cut rather than published
- [ ] Sequenced, then cut again — each survivor does something its neighbours do not
- [ ] 9–14 final images
- [ ] Consent confirmed for every recognisable person
- [ ] Archive weakness raised in writing if the set is thin
- [ ] Rejects retained, not deleted

## References

- **`art-direction`** (this framework) — the six image roles and the sequencing
  rhythm that Pass 2 and Pass 4 fill against
- **ImageMagick** — the average-colour scan used in Pass 3
  <https://imagemagick.org/script/command-line-options.php>
- **Digital Personal Data Protection Act, 2023 / DPDP Rules, 2025** — why Pass 5
  is a legal gate and not a courtesy, for photographs of identifiable people
  <https://www.pib.gov.in/PressNoteDetails.aspx?NoteId=156054&ModuleId=3&reg=3&lang=2>
- **web.dev — image sizing guidance**, the source of the 2000 px long-edge floor
  for retina heroes <https://web.dev/articles/serve-responsive-images>

**Not sourced — written for this framework:** the five-pass procedure, the Pass 1
reject list, the "faces reacting beat faces posing" rule, the grade-consistency
axes, the archive-weakness signals, and the recommendation to reduce case-study
count rather than pad.
