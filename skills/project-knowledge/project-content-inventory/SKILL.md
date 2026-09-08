---
name: project-content-inventory
version: 1.0.0
description: |
  What content and media actually exist for this photography site, what is
  missing, and who must supply it. Use before estimating the project, before
  designing around content that may not exist, and to track the gap to launch.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## Project: Content Inventory

**The most common way a portfolio project fails is that the design is finished
and the content is not.** For a photography studio the failure has a specific
shape: the images exist in abundance, and everything *around* them — the
selection, the consent, the words, the music licences — does not.

This skill tracks the gap. **It is a live document**; update it as content
arrives.

> **Before running anything:** load `project-photographer-brand`. The Instagram
> grid — the primary evidence of what exists — could not be read, which makes
> this inventory an estimate rather than a count.

### The one advantage, and the one trap

**Advantage:** unlike an agency or an event organiser, this client *is* the
photographer. The archive is theirs, at full resolution, with RAWs. There is no
sourcing problem and usually no licensing problem for their own frames.

**Trap:** abundance is not readiness. A studio with 80,000 frames still has zero
publishable case studies until someone culls, grades, gets consent, and writes.
**The bottleneck is selection and permission, not supply** — and clients
consistently mistake having the photos for having the content.

### Status as of 2026-09-08

| Asset | Status |
|---|---|
| Photograph archive | **Unassessed** — location, volume, and organisation unknown |
| Instagram grid | **Not readable** — login wall. Needs a human with account access |
| Films / reels | **Unassessed** — and their music is uncleared until proven otherwise |
| Music licences | **None known** — blocking for every published film → `film-showcase` |
| Signature definition | **Not derived** — needs 100+ photographer-selected frames → `signature-style` |
| Case-study text | **None exists** — must be written from interviews |
| Testimonials | **None collected** |
| Consent records | **None known** — likely nothing formal exists |
| Second-shooter assignments | **Unknown** — a rights gap until confirmed → `image-rights-and-credit` |
| Packages and prices | **Unknown** — blocking for `/pricing/` |
| Venue list | **Unknown** — blocking for venue pages → `vendor-network` |
| Logo / brand assets | Unknown |
| Photographer's own portrait | Unknown |
| Written copy | None |

**Nothing needed for launch currently exists in usable form.** That is normal at
this stage and it is the single biggest schedule risk. Say so early — it is
cheaper as an estimate than as a slip.

### Required for launch

| Item | Quantity | Owner | Blocking? |
|---|---|---|---|
| Case studies | **6–10** (12 is better) | Photographer + writer | **Yes** |
| Photographs per case study | 9–14 selected, graded → `wedding-story-arc` | Curator | **Yes** |
| Signature-tally frame set | 100+, photographer-selected | Photographer | **Yes** — everything downstream depends on it |
| Interview per case study | ~20 min | Writer | **Yes** |
| Consent per case study | Recorded, per person | Photographer | **Yes** — build fails without it |
| Teaser film per film-led case study | 45–90 s | Videographer | Yes, if film is sold |
| Music licence per published film | ID + PDF | Studio | **Yes** — build fails without it |
| Testimonials | 3–5 | Photographer | No, but weakens the site |
| Service descriptions | 2–4 | Photographer + writer | **Yes** |
| Pricing: packages, inclusions, travel rule | — | Photographer | **Yes** for `/pricing/` |
| About copy + the refusal | 1 page | Photographer + writer | **Yes** |
| Photographer's portrait | 1 good one, not a selfie | Photographer | No, but it converts |
| Venue pages | 0 at launch; 3+ within a quarter | Photographer + writer | No |
| Logo, vector | 1 | Client | **Yes** |
| Hero frame | 1 exceptional | Curator | **Yes** |
| OG default image | 1 | Designer | **Yes** |
| Licensing page terms | — | Studio | **Yes** → `image-rights-and-credit` |
| Contact details, verified | — | Client | **Yes** |

**Six case studies is the floor.** Below that the work section looks thin and the
positioning is undermined by its own evidence.

### The interview

Case-study text does not exist and cannot be written from photographs. It comes
from **one conversation per wedding**, roughly 20 minutes, with the photographer:

1. Who were they, and what did they ask you for?
2. What worried you about this one before you arrived?
3. **What went wrong, or was harder than you expected?** ← the case study lives here
4. What did you decide to do about it?
5. Which functions, how many days, which venue, what light?
6. **Which frame is the one you would keep, and why?** → the hero, and the
   signature evidence → `signature-style`
7. Who else shot it — second shooter, film team? → `image-rights-and-credit`
8. Which vendors were on the job? → `vendor-network`
9. May we name the venue? The couple? Publish these photographs and this film?

Question 3 makes it a case study rather than a gallery → `case-study-structure`.
Expect to ask it twice; the first answer is usually "it went smoothly."

Question 6 is the one photographers answer best and are rarely asked. **Record
the interviews** (with permission) — their own phrasing is better copy than
anything written afterwards → `web-copywriting`.

### Assessing the archive

When access is granted → `photo-curation`, `signature-style`:

```bash
# Volume and usable resolution
find "$ARCHIVE" -iname '*.jpg' -o -iname '*.jpeg' | wc -l
exiftool -ImageWidth -ImageHeight -q "$ARCHIVE"/**/*.jpg 2>/dev/null \
  | awk '/Image Width/ {w=$4} /Image Height/ {h=$4; if (w<2000 && h<2000) c++} END {print c+0, "under 2000px"}'

# Who shot it — a credit and assignment question, not a curiosity
exiftool -Artist -Copyright -Model -q "$ARCHIVE"/**/*.jpg 2>/dev/null | sort -u

# Are RAWs kept? The strongest proof of authorship → image-rights-and-credit
find "$ARCHIVE" \( -iname '*.cr2' -o -iname '*.cr3' -o -iname '*.nef' -o -iname '*.arw' -o -iname '*.dng' \) | wc -l

# GPS present — must be stripped from published derivatives
exiftool -GPSLatitude -q "$ARCHIVE"/**/*.jpg 2>/dev/null | grep -c GPS

# Films: length distribution tells you whether teasers exist
for f in "$ARCHIVE"/**/*.mp4; do
  printf '%s\t%s\n' "$(ffprobe -v error -show_entries format=duration -of csv=p=0 "$f")" "$f"
done | sort -n
```

**The `-Model` field matters more than it looks.** Multiple camera bodies across
one wedding usually means a second shooter, and a second shooter without a
written assignment is a rights gap → `image-rights-and-credit`.

**Report findings honestly and early.** If the archive is organised by date with
no culling and no ratings, plan for a real culling effort, not an afternoon.

### Instagram — the blocking gap

`@creative_weddings_films_latur` is the best available evidence of what exists,
and it cannot be read automatically. A fetch on 2026-09-08 returned only the
login wall, and no public directory listing corroborated the studio →
`project-photographer-brand`.

**A human with account access must provide:**

- [ ] Current bio text, display name, and link
- [ ] Post count, follower count, posting cadence
- [ ] Last 30–50 posts categorised: stills / reel / film, and which function
      → `project-shoot-catalogue`, `wedding-story-arc`
- [ ] The 10 best-performing posts, and the photographer's view of why
- [ ] Whether original full-resolution files exist for those posts
- [ ] What music is on the published reels, and whether it is licensed
      → `film-showcase`
- [ ] 100+ frames the photographer would choose themselves → `signature-style`

**Instagram exports are lower resolution than the originals and are re-encoded.**
Never plan to build the site from downloaded Instagram media — it will not
survive a full-bleed hero, and a re-encoded reel looks worse than the original at
half the bitrate → `image-optimization`, `video-on-web`.

### Tracking

Keep a per-case-study table and update it as things arrive:

```markdown
| Case study | Photos | Film | Music cleared | Interview | Text | Consent | Ready |
|---|---|---|---|---|---|---|---|
| Priya & Rohan 2026 | 12 ✓ | teaser ✓ | ART-1234 ✓ | ✓ | draft | ✓ | **yes** |
| Mehendi — Feb 2026 | 6 ✗ | — | — | ✓ | — | ✗ | no |
```

**"Ready" means the build passes** — the schema enforces the rest →
`content-collections`. Consent and music licence are both build-time gates, not
review-time reminders → `media-consent`, `film-showcase`.

### Caveats

- **Content is the critical path**, not development. A five-day build waiting six
  weeks for consent and culling is the normal shape of these projects.
- **Do not design around content that does not exist.** A layout requiring a
  4000 px full-bleed hero is a liability until the archive is confirmed.
- **The client will underestimate this effort.** Interviews, culling, consent
  chasing, and licence hunting is real work for them. Schedule it explicitly and
  send the interview questions in advance.
- **Consent may be impossible retroactively** for older weddings. Prefer recent
  ones where the couple relationship is still warm → `media-consent`.
- **Music clearance may retire films the studio is proud of.** Raise it early —
  it is a genuinely unwelcome finding and it lands better as a plan than as a
  launch-week blocker.
- **Launching with six strong case studies beats waiting for twelve.** Ship,
  then add.

### Checklist

- [ ] Archive located and access granted
- [ ] Archive assessed for volume, resolution, RAW retention, and camera bodies
- [ ] Second-shooter and film-team assignments established
- [ ] Instagram gap filled by a human with account access
- [ ] 100+ photographer-selected frames gathered for the signature tally
- [ ] 6–10 weddings selected for case studies
- [ ] Interview scheduled, conducted, and recorded for each
- [ ] Consent obtained per case study, per identifiable person, including children
- [ ] Music licence ID and file held for every published film
- [ ] Photographs culled and graded per case study
- [ ] Teasers cut for film-led case studies
- [ ] Service copy, About copy, the refusal, pricing, and logo received
- [ ] Licensing page terms written
- [ ] Contact details verified → `project-photographer-brand`
- [ ] Tracking table maintained and shared with the client
- [ ] Content critical path communicated in writing

## References

- **`photo-curation`, `signature-style`, `wedding-story-arc`,
  `case-study-structure`, `media-consent`, `film-showcase`,
  `image-rights-and-credit`, `content-collections`, `project-shoot-catalogue`**
  (this framework) — the workflows this inventory feeds
- **ExifTool** — the archive assessment commands above <https://exiftool.org/>
- **FFmpeg / ffprobe** — the film duration survey <https://ffmpeg.org/ffprobe.html>
- **Instagram — [`@creative_weddings_films_latur`](https://www.instagram.com/creative_weddings_films_latur/)**,
  the archive evidence that could not be read automatically
- **`SOURCES.md`** (this repository) — what was gathered and what was not

**Not sourced — written for this framework:** the abundance-is-not-readiness
framing, the required-for-launch table and the six-case-study floor, the nine
interview questions, the archive-assessment commands including the camera-body
rights check, the Instagram-export warning, and the tracking table.
