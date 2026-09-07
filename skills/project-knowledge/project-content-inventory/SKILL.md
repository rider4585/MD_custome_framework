---
name: project-content-inventory
version: 1.0.0
description: |
  What content and media actually exist for the Eventina site, what is missing,
  and who must supply it. Use before estimating the project, before designing
  around content that may not exist, and to track the gap to launch.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## Project: Content Inventory

**The most common way a portfolio project fails is that the design is finished
and the content is not.** Case studies need photographs, a problem statement, a
testimonial, and consent — and every one of those comes from the client.

This skill tracks the gap. **It is a live document**; update it as content
arrives.

> **Before running anything:** load `project-eventina-brand`. The Instagram grid
> — the primary evidence of what exists — could not be read, which makes this
> inventory an estimate rather than a count.

### Status as of 2026-09-07

| Asset | Status |
|---|---|
| Photograph archive | **Unassessed** — location, volume, and quality unknown |
| Instagram grid | **Not readable** — login wall. Needs a human with account access |
| Case-study text | **None exists** — must be written from interviews |
| Testimonials | **None collected** — public ratings exist but are not usable as quotes |
| Consent records | **None known** — likely nothing formal exists |
| Logo / brand assets | Unknown |
| Video | Unknown |
| Team photographs | Unknown |
| Written copy | None |

**Nothing needed for launch currently exists in usable form.** That is normal at
this stage and it is the single biggest schedule risk. Say so early → it is
cheaper as an estimate than as a slip.

### Required for launch

Minimum viable portfolio — see `brand-narrative` on selection as a premium signal.

| Item | Quantity | Owner | Blocking? |
|---|---|---|---|
| Case studies | **6–10** (12 is better) | Client + writer | **Yes** |
| Photographs per case study | 9–14 selected, graded | Client + curator | **Yes** |
| Problem + decisions per case study | Interview-derived | Writer | **Yes** |
| Consent per case study | Recorded | Client | **Yes** — build fails without it |
| Testimonials | 3–5 | Client | No, but weakens the site |
| Service descriptions | 3–6 | Client + writer | **Yes** |
| About / team copy | 1 page | Client + writer | **Yes** |
| Team photograph | 1 good one | Client | No |
| Logo, vector | 1 | Client | **Yes** |
| Hero photograph | 1 exceptional | Curator | **Yes** |
| OG default image | 1 | Designer | **Yes** |
| Contact details, verified | — | Client | **Yes** |

**Six case studies is the floor.** Below that the work section looks thin and
the premium positioning is undermined by its own evidence.

### The interview

Case-study text does not exist and cannot be written from photographs. It comes
from **one conversation per event**, roughly 20 minutes:

1. What did they ask you for?
2. What worried you about this one?
3. **What went wrong, or was harder than expected?** ← the case study lives here
4. What did you decide to do about it?
5. How many guests, how many team, how long, which venue?
6. What are you proudest of?
7. May we name the venue? The family? Publish these photographs?

Question 3 is the one that makes a case study rather than a gallery →
`case-study-structure`. Expect to ask it twice; the first answer is usually
"everything went smoothly."

**Record the interviews** (with permission). The client's own phrasing is better
copy than anything written afterwards → `web-copywriting`.

### Assessing the archive

Cannot be done until the archive is accessible. When it is → `photo-curation`:

```bash
# Volume and usable resolution
find "$ARCHIVE" -iname '*.jpg' -o -iname '*.jpeg' | wc -l
exiftool -ImageWidth -ImageHeight -q "$ARCHIVE"/**/*.jpg 2>/dev/null \
  | awk '/Image Width/ {w=$4} /Image Height/ {h=$4; if (w<2000 && h<2000) c++} END {print c+0, "under 2000px"}'

# Photographers involved — a licensing and credit question
exiftool -Artist -Copyright -q "$ARCHIVE"/**/*.jpg 2>/dev/null | sort -u

# GPS present — must be stripped before publishing
exiftool -GPSLatitude -q "$ARCHIVE"/**/*.jpg 2>/dev/null | grep -c GPS
```

**Report the finding honestly and early.** If there are not nine usable frames
for six events, that is a shoot, and a shoot has a lead time → `photo-curation`.

### Instagram — the blocking gap

`@eventina.organisers` is the best available evidence of what exists, and it
cannot be read automatically.

**A human with account access must provide:**

- [ ] Current bio text and link
- [ ] Post count, follower count, posting cadence
- [ ] Last 30 posts categorised by event type → `project-event-catalogue`
- [ ] The 10 best-performing posts, and the client's view of why
- [ ] Whether original full-resolution files exist for those posts
- [ ] Whether `@eventinaorganisers` (the old account) holds work worth reviving

**Instagram exports are lower resolution than the originals.** Do not plan to
build the site from downloaded Instagram images — they will not survive a
full-bleed hero → `image-optimization`.

### Tracking

Keep a per-case-study table and update it as things arrive:

```markdown
| Case study | Photos | Interview | Text | Consent | Testimonial | Ready |
|---|---|---|---|---|---|---|
| Sharma wedding 2026 | 12 ✓ | ✓ | draft | ✓ | ✓ | **yes** |
| Corporate — Feb 2026 | 6 ✗ | ✓ | — | ✗ | — | no |
```

**"Ready" means the build passes** — the schema enforces the rest →
`content-collections`.

### Caveats

- **Content is the critical path**, not development. A five-day build waiting six
  weeks for case studies is the normal shape of these projects.
- **Do not design around content that does not exist.** A layout requiring a
  full-bleed 4000 px hero is a liability if the archive is Instagram exports.
- **The client will underestimate this effort.** Six interviews, photo selection,
  and consent chasing is real work for them. Schedule it explicitly and give
  them the interview questions in advance.
- **Consent may be impossible retroactively** for older events. Prefer recent
  events where the client relationship is still warm → `media-consent`.
- **Launching with six strong case studies beats waiting for twelve.** Ship, then
  add.

### Checklist

- [ ] Archive located and access granted
- [ ] Archive assessed for volume, resolution, and consistency
- [ ] Photographer credits and licensing established
- [ ] Instagram gap filled by a human with account access
- [ ] 6–10 events selected for case studies
- [ ] Interview scheduled and conducted for each
- [ ] Interviews recorded with permission
- [ ] Consent obtained per event, including children
- [ ] Photographs culled and graded per event
- [ ] Service descriptions, About copy, and logo received
- [ ] Contact details verified → `project-eventina-brand`
- [ ] Tracking table maintained and shared with the client
- [ ] Content critical path communicated in writing

## References

- **`photo-curation`, `case-study-structure`, `media-consent`,
  `content-collections`, `project-event-catalogue`** (this framework) — the
  workflows this inventory feeds
- **ExifTool** — archive assessment commands above <https://exiftool.org/>
- **Instagram — [`@eventina.organisers`](https://www.instagram.com/eventina.organisers/)**,
  the archive evidence that could not be read automatically
- **`SOURCES.md`** §9 (this repository) — what was gathered and what was not

**Not sourced — written for this framework:** the required-for-launch table and
the six-case-study floor, the seven interview questions, the archive-assessment
commands, the Instagram-export resolution warning, and the tracking table.
