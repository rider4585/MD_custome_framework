---
name: motion-design
version: 1.0.0
description: |
  Design and specify scroll choreography, reveals, and page transitions that feel
  cinematic rather than gimmicky — with easing, timing, and reduced-motion safety
  defined up front. Use when adding any animation, when a site feels static or
  conversely "too much", or when choosing between CSS scroll-driven animation and
  a JS library.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## Motion Design

Motion is the cheapest way to make a site feel expensive and the cheapest way to
make it feel like a template. The difference is almost entirely **timing and
restraint**, not technique.

The governing rule of this framework: **motion must reveal structure or
relationship. Motion that only decorates is deleted.**

> **Before running anything:** load `emotional-brief` — pace is set by the event's
> emotion, and a Sangeet page and a ceremony page must not share timings.

### Method

1. **List every animation you intend**, with the question each one answers for
   the visitor. Delete any without an answer.
2. **Choose the cheapest technique that works** — the ladder below. Do not reach
   for a library first.
3. **Set timing from the register**, not from taste.
4. **Write the reduced-motion variant at the same time**, never afterwards.
5. **Measure INP and CLS** after, not before, shipping the motion.

### The technique ladder — climb only as far as needed

| Rung | Technique | Cost | Use for |
|---|---|---|---|
| 1 | CSS `transition` on hover/focus | ~0 | State feedback |
| 2 | CSS `@keyframes` + `animation-timeline: view()` | ~0, off main thread | Scroll reveals, progress, parallax |
| 3 | View Transitions API | ~0 | Page-to-page and shared-element transitions |
| 4 | `IntersectionObserver` + CSS class | tiny | Reveals where rung 2 lacks support |
| 5 | [Motion](https://motion.dev/) (MIT) | ~5–18 kB | Orchestrated sequences, spring physics, gestures |
| 6 | GSAP + ScrollTrigger | ~50 kB+ | Complex timelines. **Free since April 2025 but not open source** — you cannot fork or decompile it |
| 7 | [Lenis](https://github.com/darkroomengineering/lenis) smooth scroll | ~4 kB | Almost never — see caveats |

**Rungs 2 and 3 cover the great majority of a portfolio site.** Native
scroll-driven animations run off the main thread, so they do not degrade INP the
way scroll-event listeners do.

```css
/* Rung 2 — a scroll reveal with no JavaScript at all */
@keyframes reveal {
  from { opacity: 0; transform: translateY(2rem); }
  to   { opacity: 1; transform: none; }
}

.reveal {
  animation: reveal linear both;
  animation-timeline: view();
  animation-range: entry 10% cover 35%;
}

/* Progressive enhancement: browsers without support simply show the content */
@supports not (animation-timeline: view()) {
  .reveal { animation: none; opacity: 1; transform: none; }
}
```

### Timing and easing

| Register (from `emotional-brief`) | Duration | Easing | Stagger |
|---|---|---|---|
| Reverence | 700–900 ms | `cubic-bezier(.22,1,.36,1)` — long tail | 120 ms |
| Exuberance | 250–400 ms | `cubic-bezier(.34,1.56,.64,1)` — slight overshoot | 45 ms |
| Competence | 300–450 ms | `cubic-bezier(.4,0,.2,1)` — symmetrical | 60 ms |
| Delight | 350–500 ms | spring, low damping | 70 ms |
| Gravitas | 900–1200 ms, or none | `linear` or long ease-out | 200 ms |

Rules that hold across all registers:

- **Never animate `width`, `height`, `top`, or `left`.** Only `transform` and
  `opacity` are compositor-friendly; the rest force layout on every frame.
- **Enter slow, exit fast.** Something arriving deserves attention; something
  leaving should get out of the way.
- **Nothing over 1200 ms** unless it is a deliberate held moment. A visitor
  waiting for an animation to finish has stopped looking at the photograph.
- **Stagger caps at about six items.** Beyond that the last item is late enough
  to feel broken.

### Reduced motion is not optional

WCAG 2.2 SC 2.3.3 makes non-essential motion from interaction disableable, and
vestibular disorders make large-scale parallax genuinely nauseating.

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

**That blanket rule is a safety net, not the design.** Do the real work per
component: a reveal should become *no reveal with content visible*, not a 0.01 ms
flash. Never leave content hidden behind an animation that no longer runs —
that is the most common accessibility bug in scroll-reveal sites, and it makes
the page blank for those users.

```css
/* Correct per-component handling */
@media (prefers-reduced-motion: reduce) {
  .reveal { animation: none; opacity: 1; transform: none; }
}
```

Also guard JS: `window.matchMedia('(prefers-reduced-motion: reduce)').matches`
before initialising Motion, Lenis, or any timeline.

### Detection

```bash
# Animations with no reduced-motion guard anywhere in the file
grep -rln 'animation\|transition' src/ --include=*.css \
  | xargs grep -Ln 'prefers-reduced-motion'

# Layout-triggering properties being animated
grep -rnE 'transition:[^;]*(width|height|top|left|margin)' src/ --include=*.css

# Content hidden by default — blank-page risk if the reveal never fires
grep -rnE 'opacity:\s*0' src/ --include=*.css

# Scroll listeners that should be scroll-driven animations or IntersectionObserver
grep -rn "addEventListener('scroll'" src/ --include=*.js --include=*.astro
```

### Caveats

- **Smooth-scroll libraries (Lenis) override a native browser affordance.** They
  break `Ctrl+F` scroll-into-view, fight trackpad momentum, and can worsen INP.
  This framework's position: do not use one unless the client explicitly asks
  and accepts the trade-off in writing.
- **Parallax on mobile is usually a bug.** Small viewport, touch scrolling, and
  variable frame rate combine badly. Disable below the tablet breakpoint.
- **Autoplaying motion competes with the photographs.** On a portfolio the
  photograph should be the most interesting thing on screen.
- **Measure INP with motion running**, on a mid-range Android — not on your
  laptop. See `core-web-vitals`.
- **GSAP's licence is "free", not "open"**; if the client needs an auditable
  dependency chain, use Motion.

### Checklist

- [ ] Every animation justified by the question it answers; the rest deleted
- [ ] Lowest workable rung of the technique ladder chosen
- [ ] Durations and easings taken from the register table
- [ ] Only `transform` and `opacity` animated
- [ ] Stagger capped at six items
- [ ] Per-component `prefers-reduced-motion` variants written, not just the
      blanket reset
- [ ] No content left invisible when animations are disabled
- [ ] JS animation libraries gated behind a `matchMedia` check
- [ ] Parallax disabled on touch/mobile
- [ ] INP measured on a mid-range Android with motion active
- [ ] No smooth-scroll hijack unless explicitly signed off

## References

- **W3C — CSS Scroll-driven Animations Level 1**, `animation-timeline`,
  `view()`, `animation-range` <https://www.w3.org/TR/scroll-animations-1/>
- **MDN — `animation-timeline`** and scroll-driven animation support
  <https://developer.mozilla.org/en-US/docs/Web/CSS/animation-timeline>
- **MDN — `prefers-reduced-motion`**
  <https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion>
- **WCAG 2.2 — SC 2.3.3 Animation from Interactions (AAA), SC 2.2.2 Pause, Stop,
  Hide (A)** <https://www.w3.org/TR/WCAG22/#animation-from-interactions>
- **W3C — View Transitions API Level 1**
  <https://www.w3.org/TR/css-view-transitions-1/>
- **Motion — documentation and MIT licence guarantee**, including its own
  comparison with GSAP's licensing <https://motion.dev/docs/gsap-vs-motion>
- **Webflow — "Webflow makes GSAP 100% free" (April 2025)**, the source for
  GSAP's current terms <https://webflow.com/blog/gsap-becomes-free>
- **web.dev — Interaction to Next Paint (INP)** and main-thread cost
  <https://web.dev/articles/inp>

**Not sourced — written for this framework:** the seven-rung technique ladder,
the register-to-timing table, the six-item stagger cap, the enter-slow/exit-fast
rule, the position on smooth-scroll libraries, and the detection commands.
