---
name: page-transitions
version: 1.0.0
description: |
  Make navigation between pages feel continuous rather than like a page reload —
  View Transitions, shared-element morphs from a thumbnail to a case study, and
  the accessibility duties that come with them. Use when navigation feels abrupt,
  or when a thumbnail should expand into its page.
allowed-tools:
  - Read
  - Write
  - Edit
  - Grep
  - Glob
  - Bash
---

## Page Transitions

The signature move of a premium portfolio: click a thumbnail, and that exact
image grows into the hero of the case study. It reads as expensive and it is now
a platform feature rather than a JavaScript framework problem.

The View Transitions API does this with a handful of CSS lines. **The risk is
not implementation — it is the accessibility and state bugs that come with
intercepting navigation.**

> **Before running anything:** load `motion-design` for timing and the
> reduced-motion contract. A transition is motion and is bound by the same rules.

### Method

1. **Decide whether the transition communicates something.** Continuity between a
   thumbnail and its page: yes. A cross-fade between unrelated pages: usually not.
2. **Enable transitions** — native CSS for same-document, `<ClientRouter />` for
   cross-document in Astro.
3. **Name the shared elements** on both pages.
4. **Handle reduced motion explicitly.**
5. **Verify focus, scroll position, and announcements** after navigation.

### Enabling

**Native, no JavaScript** — works for cross-document navigation in supporting
browsers:

```css
@view-transition { navigation: auto; }
```

**Astro's `<ClientRouter />`** — broader support, adds a client-side router with
lifecycle events:

```astro
---
import { ClientRouter } from 'astro:transitions';
---
<head>
  <ClientRouter />
</head>
```

**Prefer the native `@view-transition` where it suffices.** `<ClientRouter />`
ships JavaScript and turns real navigations into client-side ones, which brings
the usual SPA problems below. Use it when you need lifecycle hooks or persisted
elements — an audio player, or a nav that must not re-render.

### Shared-element transitions

Give the same `view-transition-name` to the element on both pages. The browser
morphs between them.

```astro
<!-- Index -->
<a href={`/work/${event.id}/`}>
  <Image src={event.data.hero} alt={event.data.heroAlt}
         style={`view-transition-name: hero-${event.id}`} />
</a>

<!-- Case study -->
<Image src={event.data.hero} alt={event.data.heroAlt}
       style={`view-transition-name: hero-${event.id}`} />
```

**`view-transition-name` must be unique per page.** Two elements sharing a name
in one document abort the transition silently — the most common bug here, and it
happens the moment the same event appears twice on the index (say, in "featured"
and in the full grid). Generate names from a stable id, and audit for duplicates.

```css
::view-transition-old(root),
::view-transition-new(root) {
  animation-duration: 400ms;
  animation-timing-function: cubic-bezier(.22, 1, .36, 1);
}

/* Match the register from emotional-brief */
::view-transition-group(*) { animation-duration: 700ms; }
```

### Reduced motion

A full-page morph is exactly the kind of motion that triggers vestibular
symptoms.

```css
@media (prefers-reduced-motion: reduce) {
  ::view-transition-group(*),
  ::view-transition-old(*),
  ::view-transition-new(*) {
    animation: none !important;
  }
}
```

The page still navigates — it simply cuts. **Never leave a user without
navigation because the transition was suppressed.**

### The duties that come with intercepting navigation

Astro's `<ClientRouter />` replaces a real page load, so the browser stops doing
things it normally does for free. Each must be restored:

| Duty | Why | How |
|---|---|---|
| **Move focus** | Focus stays on the old link; keyboard users are lost | Focus the `<h1>` or a skip target on `astro:page-load` |
| **Announce the new page** | Screen readers say nothing on a soft navigation | An `aria-live="polite"` region announcing the new title |
| **Reset scroll** | Or restore it on back | Astro handles the common case; verify |
| **Re-run scripts** | Module scripts run once, not per navigation | Use `astro:page-load`, not `DOMContentLoaded` |
| **Re-init islands and observers** | `IntersectionObserver` bound to removed nodes | Re-bind on `astro:page-load` |
| **Update analytics** | Page views stop firing | Send on `astro:page-load` |

```js
document.addEventListener('astro:page-load', () => {
  // Runs on first load AND after every client-side navigation
  initScrollReveals();

  const h1 = document.querySelector('h1');
  if (h1) { h1.setAttribute('tabindex', '-1'); h1.focus({ preventScroll: true }); }

  document.getElementById('route-announcer').textContent = document.title;
});
```

```html
<div id="route-announcer" aria-live="polite" aria-atomic="true" class="visually-hidden"></div>
```

**`DOMContentLoaded` fires once and never again.** Every script written against
it silently stops working after the first client-side navigation — the
highest-frequency bug when adding `<ClientRouter />` to an existing site.

### Persisting elements

```astro
<audio transition:persist controls src="/media/showreel-audio.mp3"></audio>
<nav transition:persist="main-nav">…</nav>
```

`transition:persist` keeps the DOM node across navigation. Use it sparingly —
persisted nodes do not re-render, so stale state is easy to create.

### Detection

```bash
grep -rn 'view-transition-name' src/ --include=*.astro         # audit for duplicates
grep -rn 'DOMContentLoaded' src/ --include=*.astro --include=*.js  # will break
grep -rn 'ClientRouter' src/ --include=*.astro
grep -rn 'astro:page-load' src/ | wc -l                        # re-init hooks present
grep -rn 'aria-live' src/ --include=*.astro | grep -i 'announce\|route'
grep -rn 'view-transition' src/ --include=*.css | grep -c 'prefers-reduced-motion'
```

### Caveats

- **Support is uneven, especially for cross-document transitions.** Design so the
  page is correct without the transition — it is an enhancement, never a
  requirement.
- **Transitions add perceived latency.** A 700 ms morph is 700 ms before the
  visitor reads anything. Keep page transitions shorter than in-page reveals;
  300–450 ms is usually right even in a slow register.
- **Client-side routing breaks third-party scripts** that assume one page load.
  Audit any embed.
- **Test with a screen reader**, not only visually. A soft navigation that
  announces nothing is a serious failure and is invisible to sighted testing.
- **Do not transition between structurally unrelated pages.** Morphing the home
  hero into the privacy policy is disorienting, not delightful.

### Checklist

- [ ] Each transition justified by the continuity it communicates
- [ ] Native `@view-transition` preferred; `<ClientRouter />` only where needed
- [ ] `view-transition-name` unique per page — duplicates audited
- [ ] Durations 300–450 ms, easing from the register
- [ ] `prefers-reduced-motion` suppresses animation but never navigation
- [ ] Focus moved to the new page's heading after navigation
- [ ] Route announcer present and announcing the new title
- [ ] All init code on `astro:page-load`, none on `DOMContentLoaded`
- [ ] Observers and islands re-bound after navigation
- [ ] Analytics fire on soft navigations
- [ ] `transition:persist` used sparingly, stale state checked
- [ ] Verified with a screen reader and with keyboard only
- [ ] Page fully usable where the API is unsupported

## References

- **W3C — CSS View Transitions Module Level 1 and Level 2**
  <https://www.w3.org/TR/css-view-transitions-1/>
- **MDN — View Transition API**, `view-transition-name`, `::view-transition-*`
  pseudo-elements, and cross-document support
  <https://developer.mozilla.org/en-US/docs/Web/API/View_Transition_API>
- **Astro docs — View transitions and `<ClientRouter />`**, lifecycle events
  (`astro:page-load`, `astro:after-swap`) and `transition:persist`
  <https://docs.astro.build/en/guides/view-transitions/>
- **WCAG 2.2 — SC 2.3.3 Animation from Interactions, SC 4.1.3 Status Messages,
  SC 2.4.3 Focus Order** <https://www.w3.org/TR/WCAG22/#status-messages>
- **MDN — `prefers-reduced-motion`**
  <https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion>

**Not sourced — written for this framework:** the duties table for intercepted
navigation, the 300–450 ms page-transition recommendation as distinct from
in-page reveals, the duplicate-name warning, and the detection commands.
