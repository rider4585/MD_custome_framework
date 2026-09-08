---
name: film-showcase
version: 1.0.0
description: |
  Present wedding films on a portfolio — which cut belongs on which page, where
  to host them, how sound changes every layout decision, and the music-licensing
  trap that gets films taken down. Use when adding films or reels, when deciding
  between self-hosting and an embed, or when a videographer's work is being
  buried under the stills.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## Film Showcase

Most photographer-videographer sites are photography sites with a video page
bolted on, and the films are the higher-margin product. The reason is
structural: **stills reward scrolling and films punish it.** A gallery gives
value in 400 ms; a film asks for four minutes and a decision about sound. A site
designed entirely around scroll will always bury the films.

This skill is about the editorial and business layer. For encoding, poster
frames, autoplay policy, and the performance budget, use `video-on-web` — this
skill assumes those rules and does not repeat them.

> **Before running anything:** load `video-on-web` for the delivery constraints,
> and `project-client-brand` for whether film is actually a service being
> sold or an occasional add-on. The answer changes the entire site structure.

### Method

1. **Establish the film ladder** — which cuts exist, at what length.
2. **Place each rung** on the page type where it can actually be watched.
3. **Decide hosting** — self-host or embed — using the decision table below.
4. **Clear the music** before anything is published.
5. **Design for the sound decision**, which is the real interaction problem.
6. **Measure completion, not plays** → see "What to measure".

### The film ladder

| Cut | Length | Sound | Where it belongs | Job |
|---|---|---|---|---|
| **Ambient loop** | 6–10 s | Silent, always | Hero or section break | Texture. Not a film — it is a moving photograph → `video-on-web` |
| **Teaser** | 45–90 s | Essential | Case study, above the gallery | The hook. The only cut most visitors will finish |
| **Highlight film** | 3–6 min | Essential | Case study, after the gallery; films index | The product being sold |
| **Feature / documentary edit** | 15–40 min | Essential | Linked, not embedded | Proof of depth for the couple who is already convinced |
| **Full ceremony** | 1 hr+ | Essential | Never on the marketing site | Client delivery only → `client-gallery-delivery` |

**The teaser is the most valuable asset on a film-selling site and the one most
often missing.** A 4-minute highlight film has a completion rate in the low
single digits from cold traffic; a 60-second teaser is watched to the end and
then the highlight film gets a deliberate, sound-on click. If the videographer
does not cut teasers, that is the highest-leverage request to make of them.

**Never place a highlight film above a gallery.** The visitor has not yet decided
to spend four minutes, and a large silent player at the top of a case study
converts worse than a still hero → `art-direction`.

### Hosting: self-host or embed

| | Self-host (Astro + CDN) | Vimeo | YouTube |
|---|---|---|---|
| **Ambient loops** | ✅ Always. An embed for a 6-second silent loop is absurd | ✗ | ✗ |
| **Teasers** | ✅ Usually — small enough to control | ✅ Acceptable | ⚠️ Branding intrudes |
| **Highlight films** | ⚠️ Bandwidth and no adaptive bitrate | ✅ Best fit | ✅ If discovery matters more than control |
| **Feature edits** | ✗ | ✅ | ✅ |
| **Adaptive bitrate** | ✗ without extra tooling | ✅ | ✅ |
| **Third-party cookies / consent** | None | Fewer; `player.vimeo.com/video/ID?dnt=1` | Use `youtube-nocookie.com` |
| **Cost at scale** | Egress charges | Subscription | Free |
| **Suggested videos at the end** | N/A | Off by default | `rel=0` only limits to the same channel — it cannot be fully disabled |

**Default recommendation:** self-host ambient loops and teasers, embed everything
longer, and **facade-load every embed** — render a poster image and load the
iframe on click. An unfaçaded YouTube embed costs roughly half a megabyte and a
significant LCP penalty before the visitor has decided to watch anything →
`core-web-vitals`, `performance-budget`.

```astro
---
const { id, poster, title } = Astro.props;
---
<button class="facade" data-video={id} aria-label={`Play film: ${title}`}>
  <img src={poster} alt="" width="1280" height="720" loading="lazy" decoding="async" />
  <span class="facade__play" aria-hidden="true"></span>
</button>
<script>
  document.querySelectorAll('.facade').forEach((el) => {
    el.addEventListener('click', () => {
      const f = document.createElement('iframe');
      f.src = `https://player.vimeo.com/video/${el.dataset.video}?dnt=1&autoplay=1`;
      f.allow = 'autoplay; fullscreen; picture-in-picture';
      f.title = el.getAttribute('aria-label');
      f.loading = 'lazy';
      el.replaceWith(f);
    });
  }, { once: true });
</script>
```

Note the `<button>`: a facade is an interactive control and must be focusable and
announced. A `<div>` with a click handler fails keyboard access outright →
`accessibility`.

### Sound is the design problem

A wedding film is a piece of *music* with pictures on it. The edit is cut to the
track, the emotional beats are the track's beats, and muted playback destroys
roughly all of its effect. Yet **no browser will autoplay with sound**, and no
visitor wants a site that suddenly makes noise.

Rules that follow:

- **Never autoplay with sound.** Browsers block it, and where they do not, it is
  the fastest route to a closed tab.
- **Autoplay muted is for ambient loops only** — cuts with a soundtrack must
  start on an explicit click, so the visitor hears the track from its first bar.
  A muted film that unmutes 20 seconds in has already lost its opening.
- **Label the control for sound**, not just play: "Play film — with sound" sets
  the expectation and measurably reduces the immediate-pause reflex.
- **Pause everything else on play.** Two audio sources at once is a bug.
- **Captions or a transcript are required** where speech carries meaning — vows,
  speeches, an interview cut. `<track kind="captions">` on self-hosted video;
  uploaded caption files on an embed. Purely musical films need no captions but
  should state that they contain music → `accessibility`.
- **Respect `prefers-reduced-motion`** for ambient loops: show the poster frame
  instead of playing → `motion-design`.

### Music licensing — the trap

This is the failure that costs a studio its back catalogue, and it is
consistently underestimated.

**A commercially released song used in a wedding film that is published on a
public portfolio is a public, commercial use.** It is not covered by the couple's
personal-use assumption, and "we credited the artist" has no legal effect. On
YouTube the practical outcome is Content ID: the film is muted, blocked in some
territories, or monetised by the rights holder. On a self-hosted site the outcome
is a takedown demand.

**The rule for this framework: no film goes on the public site unless its music
is one of —**

1. **Licensed from a production-music library** for commercial/web use — Artlist,
   Musicbed, Epidemic Sound, Soundstripe and similar. Keep the licence PDF and
   its ID with the asset → `asset-workflow`.
2. **Creative Commons with a compatible clause.** Check `NC` carefully — a
   portfolio that sells services is commercial use, so `CC BY-NC` does **not**
   work. `CC BY` and `CC0` do, with attribution where required.
3. **Original score**, with a written assignment or licence from the composer.
4. **Directly licensed** from the rights holder, in writing.

**A different cut may be delivered to the couple privately** with whatever track
they chose — private delivery is a different use with a different risk profile,
and it is why `client-gallery-delivery` exists as a separate, unindexed surface.
Do not let a privately-scored cut leak onto the public site.

```yaml
# Required alongside every published film — see asset-workflow
film: sharma-highlight
track: "Weightless — Artlist"
licence_id: "ART-XXXXXXXX"
licence_scope: "Worldwide, perpetual, web + social, commercial"
licence_file: "docs/licences/art-xxxxxxxx.pdf"
cleared_by: "Name"
cleared_on: 2026-09-08
```

**No `licence_id`, no publish.** Make it a build-time check, the same way consent
is gated → `media-consent`.

### Films and stills on the same page

- **Interleave, do not segregate.** A films-only page gets a fraction of the
  traffic of the work index; the teaser belongs inside the case study where the
  visitor already is.
- **The poster frame must be a frame from the film**, graded to match the stills
  around it. A mismatched poster is the most visible grading error on the page →
  `color-mood`.
- **Give the player the same width as the gallery's full-bleed images**, so it
  reads as part of the sequence rather than an embed dropped in → `editorial-layout`.
- **Mark up films with `VideoObject`.** Video is one of the few schema types that
  reliably produces a visible rich result → `structured-data`.

### What to measure

Plays are a vanity number — a facade click is one intent, not one viewing.

| Metric | Why |
|---|---|
| **25 / 50 / 75 / 100% completion** | Where films lose people. A cliff at 15 s is an opening problem |
| **Teaser → highlight click-through** | Whether the ladder works |
| **Enquiries from pages containing a film vs. not** | The only number that pays |
| **Facade click rate** | Whether the poster frame is doing its job |

Use a privacy-respecting analytics tool and disclose it → `enquiry-conversion`.

### Caveats

- **Licensing terms change and vary by library and territory.** The four routes
  above are a policy, not legal advice; read the actual licence, and get a
  lawyer's view before a large back catalogue is published.
- **Content ID is not a legal judgement** and can fire on licensed tracks. Keep
  the licence ID to dispute it.
- **Embed players change their defaults.** Verify the privacy parameters against
  current provider documentation before relying on them.
- **Bandwidth costs are real** when self-hosting long films from a CDN with
  metered egress. Model it before choosing self-hosting for anything over a
  minute.
- **The film ladder assumes a studio that shoots film seriously.** A photographer
  who delivers one reel a year should have one film on the site, not a ladder.

### Checklist

- [ ] Film ladder defined; a teaser exists for every published highlight film
- [ ] No highlight film placed above a gallery
- [ ] Hosting chosen per rung using the decision table
- [ ] Every embed facade-loaded behind a real `<button>` with a label
- [ ] Nothing autoplays with sound; ambient loops muted and reduced-motion aware
- [ ] Play control names sound explicitly
- [ ] Captions or transcript wherever speech carries meaning
- [ ] Every published film has a licence ID, scope, and stored licence file
- [ ] Build fails on a film without cleared music
- [ ] Privately-scored client cuts kept off the public site
- [ ] Poster frames taken from the film and graded to match the stills
- [ ] `VideoObject` structured data emitted from the collection
- [ ] Completion and click-through measured, not plays

## References

- **This framework's `video-on-web`** — encoding, `+faststart`, poster frames,
  autoplay policy, and the performance budget this skill assumes
- **MDN — Autoplay guide for media and Web Audio APIs**, on why muted is the only
  reliable autoplay
  <https://developer.mozilla.org/en-US/docs/Web/Media/Autoplay_guide>
- **web.dev — "Lazy-load third-party resources with facades"**, the facade
  pattern and its measured cost savings
  <https://web.dev/articles/third-party-facades>
- **WCAG 2.2 — SC 1.2.2 Captions (Prerecorded), SC 1.4.2 Audio Control**
  <https://www.w3.org/TR/WCAG22/>
- **Creative Commons — NonCommercial interpretation**, why `CC BY-NC` fails on a
  site that sells services
  <https://creativecommons.org/faq/#does-my-use-violate-the-noncommercial-clause-of-the-licenses>
- **YouTube Help — How Content ID works**
  <https://support.google.com/youtube/answer/2797370>
- **Vimeo — Do Not Track (`dnt`) player parameter**
  <https://help.vimeo.com/hc/en-us/articles/12426199699857>
- **Schema.org — `VideoObject`** and **Google Search Central — Video structured
  data** <https://developers.google.com/search/docs/appearance/structured-data/video>

**Not sourced — written for this framework:** the five-rung film ladder and its
placement rules, the teaser-first argument, the hosting decision table's
recommendations, the sound-first design rules, the four-route music policy with
build-time gating, and the measurement table.
