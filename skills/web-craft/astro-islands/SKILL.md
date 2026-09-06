---
name: astro-islands
version: 1.0.0
description: |
  Decide what needs client-side JavaScript and hydrate only that — client
  directives, the props boundary, shared state between islands, and the static
  alternative. Use when adding interactivity, when the JS bundle grows, or when
  something needs to react to the user.
allowed-tools:
  - Read
  - Write
  - Edit
  - Grep
  - Glob
  - Bash
---

## Islands

An island is a component that ships JavaScript to the browser. Everything else on
the page is static HTML. **The value of the architecture is entirely in how few
islands there are** — a portfolio that hydrates its whole page has paid Astro's
cost and taken none of its benefit.

The discipline: **every island is a deliberate, justified exception.**

### Method

1. **Try static first.** Most "interactive" needs are solved by HTML and CSS.
2. **If JS is needed, try a `<script>` tag** before a framework component.
3. **If a component is needed, pick the narrowest client directive.**
4. **Keep the island small** — hydrate the widget, not the page.
5. **Measure what shipped.**

### The escalation ladder

| Need | Solution | JS cost |
|---|---|---|
| Accordion, disclosure | `<details>` / `<summary>` | 0 |
| Modal, lightbox | `<dialog>` + `showModal()` | ~10 lines |
| Tabs | Anchor links, or a small script | ~0 |
| Scroll reveal | `animation-timeline: view()` | 0 |
| Nav toggle | `<script>` + a class | ~10 lines |
| Form validation | Native constraint validation + `:user-invalid` | 0 |
| Filterable gallery | `<script>` toggling `hidden` | ~30 lines |
| Complex stateful widget | Framework island | 30–80 kB |

**Most of a portfolio sits in the top rows.** A filterable gallery does not need
React. Reach for a framework component only when state is genuinely complex —
a multi-step enquiry form with dependent fields, for instance.

```astro
<!-- Native accordion — no JS, accessible, keyboard-operable for free -->
<details>
  <summary>What is included in wedding planning?</summary>
  <p>Venue liaison, vendor coordination, day-of management…</p>
</details>
```

### Client directives

| Directive | Hydrates | Use for |
|---|---|---|
| *(none)* | Never | The default. Static HTML |
| `client:visible` | On scroll into view | **Best default for an island.** Below-the-fold widgets |
| `client:idle` | When the main thread is free | Above-the-fold, non-urgent |
| `client:load` | Immediately | Only if it must work before paint |
| `client:media="(min-width: 48rem)"` | At a breakpoint | Desktop-only interactions |
| `client:only="react"` | Client-only, no SSR | Last resort — no HTML for crawlers, and it flashes |

```astro
---
import EnquiryForm from '../islands/EnquiryForm.jsx';
import GalleryFilter from '../islands/GalleryFilter.jsx';
---
<GalleryFilter client:visible events={events} />
<EnquiryForm client:idle services={serviceNames} />
```

**`client:visible` should be the default.** A gallery filter three screens down
does not need to hydrate on load.

**Avoid `client:only`.** It renders nothing server-side, so crawlers see an empty
element and users see a flash. On a portfolio whose whole point is being indexed
and fast, that is a poor trade.

### The props boundary

Props cross from build-time to browser and are **serialised into the HTML**.

- **Only serialisable values pass.** No functions, no class instances, no
  `Date` methods surviving.
- **Everything you pass is visible in the page source.** Never pass secrets.
- **Every byte is shipped twice** — once in the HTML payload, once in whatever
  the component re-renders. Passing a 400-event array to a filter island bloats
  the page.

```astro
<!-- BAD: whole objects, including data the island never reads -->
<GalleryFilter client:visible events={allEvents} />

<!-- GOOD: only the fields the island needs -->
<GalleryFilter
  client:visible
  events={allEvents.map(e => ({
    id: e.id, title: e.data.title, type: e.data.eventType,
  }))}
/>
```

### Islands do not share state

Each island is a separate root. They cannot see each other's context. Options,
in order of preference:

1. **Do not need shared state.** Usually achievable by moving the boundary.
2. **One island wrapping both widgets**, if they are adjacent.
3. **Custom events on `document`** for loose coupling.
4. **Nano Stores** (MIT, ~1 kB) — the framework-agnostic option Astro documents.

```js
// Loose coupling with no library
document.dispatchEvent(new CustomEvent('filter:change', { detail: { type: 'wedding' } }));
document.addEventListener('filter:change', e => { /* … */ });
```

**If you find yourself needing a global store, question the boundary first.** It
usually means one island should have been drawn larger, or the interaction should
be a page navigation.

### Slots and children

Passing static content into an island keeps it out of the JS bundle — the
children render server-side:

```astro
<Tabs client:visible>
  <div slot="tab-1"><ExpensiveStaticContent /></div>
</Tabs>
```

The content in the slot stays HTML. This is the main trick for keeping islands
small.

### Detection

```bash
grep -rn 'client:' src/ --include=*.astro                     # every island, reviewed
grep -rn 'client:load' src/ --include=*.astro                 # justify each
grep -rn 'client:only' src/ --include=*.astro                 # should be zero
npm run build && ls -la dist/_astro/*.js | awk '{s+=$5} END {print s/1024 " kB JS"}'
grep -c '<script' dist/index.html
grep -o 'astro-island' dist/**/*.html | wc -l                 # island count in output
```

**Budget: under 60 kB of JavaScript on any page**, and zero on pages with no
interactivity (about, privacy, most case studies). See `performance-budget`.

### Caveats

- **Every framework you use ships its own runtime.** React and Preact on one site
  means two runtimes. Pick one; **Preact is ~4 kB against React's ~45 kB** and is
  usually sufficient for a portfolio's needs.
- **`client:visible` still costs when it fires**, so a heavy island entering the
  viewport can spike INP. Keep islands light regardless of the directive.
- **Islands break scoped styles.** `.astro` scoped CSS does not reach inside a
  framework component; see `css-architecture`.
- **Do not hydrate to fix a styling problem.** It is the most expensive possible
  fix.
- **Interactivity must degrade.** A filter that hides content with JS must show
  everything if JS fails.

### Checklist

- [ ] Every island justified against the escalation ladder
- [ ] Static HTML/CSS solutions preferred and exhausted first
- [ ] `client:visible` used by default; `client:load` justified individually
- [ ] No `client:only` unless unavoidable and documented
- [ ] Props trimmed to the fields the island reads
- [ ] No secrets in props
- [ ] Islands do not require shared state, or use events/Nano Stores
- [ ] Static children passed via slots, not re-rendered client-side
- [ ] One UI framework across the whole site
- [ ] Per-page JS under 60 kB; zero on non-interactive pages
- [ ] Content still accessible with JS disabled

## References

- **Astro docs — Islands architecture**
  <https://docs.astro.build/en/concepts/islands/>
- **Astro docs — Template directives (`client:*`)**
  <https://docs.astro.build/en/reference/directives-reference/#client-directives>
- **Astro docs — Sharing state between islands (Nano Stores)**
  <https://docs.astro.build/en/recipes/sharing-state-islands/>
- **MDN — `<details>`, `<dialog>`, and constraint validation**, the zero-JS
  alternatives on the ladder
  <https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/details>
- **MDN — `CustomEvent`** <https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent>
- **web.dev — Interaction to Next Paint (INP)**, why hydration cost is a UX metric
  <https://web.dev/articles/inp>

**Not sourced — written for this framework:** the escalation ladder, the
`client:visible`-by-default position, the props-trimming rule, the 60 kB per-page
budget, the Preact-over-React recommendation, and the detection commands.
