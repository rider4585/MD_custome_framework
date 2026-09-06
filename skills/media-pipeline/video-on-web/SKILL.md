---
name: video-on-web
version: 1.0.0
description: |
  Use video on a portfolio without wrecking performance or accessibility —
  hero loops, highlight reels, encoding, poster frames, autoplay rules, and when
  to use a still instead. Use when adding any video, or when a client asks for a
  "cinematic video background".
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## Video on the Web

Event companies have video, and they want it on the home page. Video is also the
single fastest way to destroy the performance of an otherwise good site, and a
badly-implemented hero loop can cost more than every image on the page combined.

**The default answer is a still image.** Video must earn its place.

> **Before running anything:** check `performance-budget`. A hero video consumes
> most of a page's byte budget on its own, so the decision is a trade, not an
> addition.

### Method

1. **Ask what the video does that a still cannot.** Motion of fabric, crowd
   energy, a lighting reveal — real answers. "It looks premium" is not.
2. **Choose the delivery pattern** from the table below.
3. **Encode properly** — most source files are 10–50× larger than needed.
4. **Never let video be the LCP element.**
5. **Handle reduced motion, data saver, and no-autoplay.**

### Patterns

| Pattern | Weight | Use |
|---|---|---|
| **Still image** | ~150 kB | The default. Choose this unless there is a specific answer to step 1 |
| **Short muted loop** (4–8 s, no audio) | 400 kB–1.5 MB | Hero texture — fabric, lights, crowd |
| **Poster + click to play** | ~150 kB until clicked | Highlight reels, testimonials with audio |
| **Embedded YouTube/Vimeo** | 0 kB until clicked, if façaded | Long films. **Never a raw iframe** |
| **Background video with audio** | — | Never |

### Encoding

Source files from a videographer are typically 200 MB+ ProRes or high-bitrate
H.264. Shipping that is not an option.

```bash
# Muted hero loop: 1280px wide, no audio, web-optimised, ~600 kB for 6 seconds
ffmpeg -i source.mov \
  -t 6 \
  -vf "scale=1280:-2,fps=24" \
  -c:v libx264 -profile:v main -crf 28 -preset slow \
  -movflags +faststart \
  -an \
  hero-loop.mp4

# Modern codec, roughly 30% smaller — serve first with the MP4 as fallback
ffmpeg -i source.mov -t 6 -vf "scale=1280:-2,fps=24" \
  -c:v libvpx-vp9 -crf 36 -b:v 0 -an hero-loop.webm

# Poster frame from 2 seconds in
ffmpeg -i source.mov -ss 00:00:02 -frames:v 1 -q:v 2 poster.jpg
```

Key flags, and why:

- **`-movflags +faststart`** moves the index to the front so playback can begin
  before the file finishes downloading. Omitting it is the most common encoding
  mistake.
- **`-an`** strips audio. A muted loop with an audio track wastes bytes and can
  block autoplay policies.
- **`fps=24`** — 60 fps doubles the size for no benefit in a background loop.
- **`-crf 28`** for a background loop; 23 for content the viewer actually watches.

**Cap a hero loop at 8 seconds and 1.5 MB.** Beyond that, use a still.

### Markup

```html
<video
  autoplay muted loop playsinline
  poster="/media/hero-poster.jpg"
  preload="none"
  aria-hidden="true"
  width="1280" height="720">
  <source src="/media/hero-loop.webm" type="video/webm">
  <source src="/media/hero-loop.mp4"  type="video/mp4">
</video>
```

- **`playsinline`** — without it iOS Safari opens fullscreen on play.
- **`muted`** is required for autoplay to be permitted at all.
- **`preload="none"`** with a poster: the poster paints immediately and the video
  arrives after. This is what keeps the video off the LCP path.
- **`aria-hidden="true"`** for a purely decorative loop — it carries no
  information and should not be announced.
- **`width`/`height`** to reserve space and avoid CLS.

**A decorative hero loop must never be the LCP element.** Make the poster image
or the headline the LCP candidate, and verify it — see `core-web-vitals`.

### Reduced motion, and data

Autoplaying video is motion. WCAG SC 2.2.2 requires that anything auto-playing
for more than five seconds can be paused, and users who set reduced motion have
asked not to see it.

```js
const reduce = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
const saveData = navigator.connection?.saveData === true;
const slow = /2g/.test(navigator.connection?.effectiveType ?? '');

if (reduce || saveData || slow) {
  video.removeAttribute('autoplay');
  video.pause();          // poster remains visible — the page still works
} else {
  video.play().catch(() => { /* autoplay refused; poster stands in */ });
}
```

Always `catch` the `play()` promise. Autoplay refusal is normal, not exceptional,
and an unhandled rejection is a console error on a large share of visits.

**`saveData` and `effectiveType` matter a great deal in this market.** Respecting
them is not an edge case for an audience on Indian mobile data.

### Captions and audio

Any video with speech needs captions — WCAG SC 1.2.2. A testimonial video without
them excludes deaf viewers and everyone scrolling with sound off, which is most
people.

```html
<video controls poster="/media/testimonial-poster.jpg" preload="none">
  <source src="/media/testimonial.mp4" type="video/mp4">
  <track kind="captions" src="/media/testimonial.en.vtt" srclang="en" label="English" default>
  <track kind="captions" src="/media/testimonial.mr.vtt" srclang="mr" label="मराठी">
</video>
```

**Never autoplay audio.** It fails WCAG SC 1.4.2 and visitors leave.

### Third-party embeds

A raw YouTube iframe loads roughly 500 kB–1 MB of JavaScript and sets tracking
cookies **before anyone presses play** — which also drags a consent banner into
scope. Use a façade: poster image plus play button, swapping in the iframe on
click.

`youtube-nocookie.com` reduces but does not eliminate the tracking concern.

### Detection

```bash
grep -rn '<video' src/ --include=*.astro | grep -v playsinline           # iOS fullscreen bug
grep -rn '<video' src/ --include=*.astro | grep autoplay | grep -v muted # will not autoplay
grep -rn 'preload="auto"' src/ --include=*.astro                          # eager video download
grep -rn 'youtube.com/embed' src/ --include=*.astro                       # raw iframe, no façade
grep -rn '<video' src/ --include=*.astro | grep -v 'poster='              # no poster
find public/media -name '*.mp4' -size +2M                                 # over budget
ffprobe -v error -show_entries format=duration,size -of csv public/media/*.mp4
```

### Caveats

- **Clients will send a 3-minute wedding film for the home page.** Offer the
  poster-and-play pattern with the film on its own page. Show the byte number;
  it is a more persuasive argument than taste.
- **Video makes hosting decisions.** Large files on a static host may hit limits
  or egress costs; consider a video host with a façade. See `static-deploy`.
- **Autoplay policies vary and change.** Never build a layout that is broken if
  playback never starts — the poster must be a complete design.
- **Video is disproportionately expensive on metered data.** In this market,
  respect `saveData` rather than treating it as optional.

### Checklist

- [ ] Video justified against a still image, specifically
- [ ] Hero loop ≤ 8 s, ≤ 1.5 MB, no audio track
- [ ] Encoded with `+faststart`, `-an`, capped fps, sane CRF
- [ ] WebM offered before MP4
- [ ] `muted`, `playsinline`, `loop`, `preload="none"`, poster set
- [ ] `width`/`height` present; no layout shift
- [ ] Video is not the LCP element — verified, not assumed
- [ ] Decorative loops `aria-hidden`
- [ ] Reduced motion, `saveData`, and slow connections all fall back to poster
- [ ] `play()` promise rejection handled
- [ ] Captions on all speech, in every shipped language
- [ ] No autoplaying audio anywhere
- [ ] Third-party embeds façaded, not raw iframes

## References

- **MDN — `<video>`, `autoplay` policies, and the `playsinline` attribute**
  <https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/video>
- **MDN — Autoplay guide for media and Web Audio**
  <https://developer.mozilla.org/en-US/docs/Web/Media/Guides/Autoplay>
- **WCAG 2.2 — SC 1.2.2 Captions (Prerecorded), SC 1.4.2 Audio Control,
  SC 2.2.2 Pause, Stop, Hide** <https://www.w3.org/TR/WCAG22/#audio-control>
- **web.dev — "Video and Core Web Vitals"** and the poster/LCP interaction
  <https://web.dev/articles/lcp>
- **FFmpeg documentation** — `libx264`, `libvpx-vp9`, `-movflags +faststart`
  <https://ffmpeg.org/ffmpeg-formats.html>
- **MDN — `NetworkInformation.saveData` and `effectiveType`**
  <https://developer.mozilla.org/en-US/docs/Web/API/NetworkInformation/saveData>

**Not sourced — written for this framework:** the pattern table, the 8 s / 1.5 MB
hero cap, the specific FFmpeg recipes, the combined reduced-motion/saveData gate,
and the detection commands.
