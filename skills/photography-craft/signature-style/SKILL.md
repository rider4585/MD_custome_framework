---
name: signature-style
version: 1.0.0
description: |
  Define and defend the one visual signature that makes a photographer
  hireable — the recurring choice a stranger could pick out of a hundred
  galleries. Use before culling a portfolio, before writing an about page, when
  the work "looks good but looks like everyone else", or when the client asks
  to add a shooting style they do not actually shoot.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
---

## Signature Style

A wedding photographer is not chosen because their photographs are sharp. Every
working photographer's photographs are sharp. They are chosen because a couple
looked at a gallery and thought *I want my day to look like that* — which means
the gallery had a **look**, and the look was consistent enough to be a promise.

A portfolio without a signature is a competence display. A competence display
competes only on price.

> **Before running anything:** load `project-photographer-brand`. The signature
> is a claim about a real person's work — it must be derived from their actual
> photographs and confirmed by them, never assigned by a designer.

### Method

1. **Gather 100+ frames the photographer chose themselves** — their Instagram
   grid, their pinned galleries, their own favourites. Not client favourites.
   What a photographer repeatedly posts is what they are trying to become.
2. **Tally the recurring choices** across the axes below. You are looking for
   choices that appear in **more than 60%** of frames. Below that it is range,
   not signature.
3. **Name the signature in one sentence** that a competitor could not also say.
4. **Find the refusal.** Every real style is defined as much by what it excludes.
   A photographer who cannot name a shot they will not take has no style yet.
5. **Test it destructively** — see "The mixed-grid test" below.
6. **Write it into `project-photographer-brand`**, and make every culling
   decision answer to it → `photo-curation`.

### The axes to tally

Score each axis across the sample. The signature is the two or three axes where
the photographer is *unusually consistent*, not the average of all of them.

| Axis | Range you are looking for |
|---|---|
| **Light** | Backlit / available-only / hard flash / mixed ambient. The single most legible signal |
| **Distance** | Wide-environmental vs. close-intimate. Where do they stand? |
| **Moment** | Anticipation (before) / peak action / aftermath (the crying aunt, 3 seconds later) |
| **Posing** | Directed / prompted / observed. "Candid" is usually prompted; say which |
| **Colour** | Warm-film / true-neutral / cool-desaturated / high-contrast. See `color-mood` |
| **Black and white** | Never / occasional / a stated fraction of every delivery |
| **Frame** | Clean-negative-space vs. layered-through-foreground |
| **Motion** | Frozen vs. deliberate drag — a photographer who drags the shutter at the baraat is telling you something |

**The most common finding is that the photographer's real signature is not the
one they describe on their about page.** People describe the style they admire;
their frames show the style they have. Show them the tally, not your opinion.

### Naming it

A usable signature sentence has a **light choice, a distance choice, and a moment
choice**, and survives a swap test — replace the photographer's name with a
competitor's, and if the sentence still reads as true, it is not a signature.

Fails the swap test:

> "Candid, emotional storytelling that captures your day as it truly happened."

Passes:

> "Backlit and close, shot into the sun at the end of the ceremony, and always
> the ten seconds *after* the moment everyone else photographs."

The second is a promise you can be held to. That is the point — a signature has
to be falsifiable, or a couple cannot tell whether they got it.

### The refusal

Ask directly: **what job do you turn down, and what shot do you refuse to take?**

Typical honest answers: no flash on the ceremony; no group photos beyond a fixed
list; no drone; no "Pinterest recreation" shot lists; no more than one wedding a
weekend. The refusal goes on the site — usually on the about or process page,
phrased as a preference rather than a complaint → `web-copywriting`.

**A refusal is a conversion asset, not a limitation.** It filters enquiries down
to the couples who want that photographer specifically, which is the only kind of
enquiry worth having → `enquiry-conversion`.

### The mixed-grid test

The destructive test, and the fastest way to settle an argument about style.

1. Take 12 of the photographer's frames.
2. Mix in 12 frames from three competitors, unlabelled.
3. Ask three people who have not seen any of it to sort them by photographer.

If they cannot pull the client's 12 back out at better than chance, the signature
is not yet visible in the work. That is a **finding to report**, not a problem to
paper over with layout — no grid, typeface, or transition creates a style that
the photography does not have.

When the test fails, the honest options are: cull much harder to the 40% that
*does* share a look, or tell the photographer that the portfolio's problem is
upstream of the website.

### How the signature drives the site

| Decision | Driven by |
|---|---|
| Which 30 frames are on the home page | The signature, ruthlessly → `photo-curation` |
| Grade target for the whole set | The colour axis → `color-mood`, `image-optimization` |
| Whether galleries are large-and-slow or dense-and-fast | The distance axis → `gallery-patterns` |
| Hero treatment and crop discipline | The frame axis → `art-direction` |
| About-page copy and the refusal | This skill → `brand-narrative` |

### Anti-patterns

- **The everything portfolio.** Weddings, newborns, products, and real estate on
  one site. It reads as "available", not "chosen". Split the domains or drop one.
- **Style by preset name.** "Shot on Mastin Kodak Portra 400 preset" is a tool,
  not a signature. Couples do not buy presets.
- **Borrowed adjectives.** "Timeless", "authentic", "cinematic" appear on
  essentially every wedding photography site in the world and therefore carry no
  information → `web-copywriting`.
- **Designing the signature.** The website cannot grant a look the archive lacks.
  Report the gap.
- **Signature drift after launch.** New work gets uploaded without culling and
  the look dilutes within a year. Gate additions on the signature → `asset-workflow`.

### Caveats

- **A photographer in year two often genuinely has range rather than signature**,
  and forcing a premature signature can be wrong. In that case, name the axis
  they are *most* consistent on and build there, but say plainly that this is a
  bet, not an observation.
- **Videography usually has a different signature from stills** — the same person
  may be observational with a camera and highly constructed in an edit. Do not
  assume one sentence covers both → `film-showcase`.
- **Cultural context bounds the axes.** A style built around available light does
  not survive a night baraat or an indoor mandap; a signature that cannot be
  delivered at the client's actual venues is a marketing problem waiting to
  become a refund.
- **This is a model, not a measurement.** The 60% threshold and the mixed-grid
  test are this framework's opinions, argued above, not established practice.

### Checklist

- [ ] 100+ photographer-selected frames gathered, not client-selected
- [ ] Every axis tallied; the two or three consistent ones identified
- [ ] Signature stated in one falsifiable sentence
- [ ] Sentence passes the swap test
- [ ] The refusal named, in the photographer's own words
- [ ] Mixed-grid test run with three naive sorters, result recorded
- [ ] A failed test reported as a finding, not hidden by design
- [ ] Signature confirmed by the photographer, dated, in `project-photographer-brand`
- [ ] Culling, grading, and gallery decisions all trace back to it
- [ ] A rule written for how new work is admitted after launch

## References

- **Schema.org / this framework's `photo-curation` and `color-mood`** — where the
  signature becomes selection and grade decisions
- **Nielsen Norman Group — "Emotional Design"**, on visceral response preceding
  reflective judgement, which is why the look is decided before the copy is read
  <https://www.nngroup.com/articles/theory-user-delight/>
- **Butterick's *Practical Typography*** — the argument that consistency of
  restraint is itself expressive <https://practicaltypography.com/>

**Not sourced — written for this framework:** the eight-axis tally, the 60%
threshold, the one-sentence swap test, the refusal-as-conversion-asset claim, the
mixed-grid test, and the anti-patterns. These are argued opinions, not measured
findings.
