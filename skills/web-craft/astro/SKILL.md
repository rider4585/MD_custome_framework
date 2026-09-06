---
name: astro
version: 1.0.0
description: |
  Build and structure an Astro site — project layout, routing, layouts and slots,
  the server/client boundary, and the conventions that keep a portfolio fast. Use
  when starting an Astro project, adding pages, or when something works in dev
  but not in the build.
allowed-tools:
  - Read
  - Write
  - Edit
  - Grep
  - Glob
  - Bash
---

## Astro

Astro renders to HTML at build time and ships **no JavaScript unless you ask for
it**. For a portfolio — mostly static content, heavy images, a handful of
interactive moments — that default is the whole reason to choose it.

The one concept that governs everything: **component frontmatter runs at build
time on the server, and the template renders to static HTML.** Confusion about
which side code runs on causes most Astro bugs.

> **Stack neutrality:** this skill is the worked example for the current stack.
> The principles — build-time rendering, an explicit hydration boundary, typed
> content — apply to any static-first framework. If the project moves, port the
> principles and rewrite the examples.

### Method

1. **Lay the project out** per the structure below.
2. **Keep everything static** until something demonstrably needs interactivity.
3. **Put shared chrome in layouts**, not repeated per page.
4. **Type your content** with collections before writing pages.
5. **Verify the built output**, not the dev server.

### Project structure

```
src/
  assets/          images processed by the build      → image-optimization
  components/      .astro components, static by default
  islands/         framework components that hydrate  → astro-islands
  content/         markdown + config                  → content-collections
  layouts/         page shells
  pages/           file-based routes
  styles/          tokens and global CSS              → css-architecture
public/            copied verbatim, NOT optimised
```

**`src/assets` versus `public` is the distinction to get right.** Files under
`src/` go through the build pipeline — hashed, optimised, transcoded. Files in
`public/` are copied byte-for-byte. Photographs in `public/` silently skip every
optimisation; see `asset-workflow`.

### The two halves of a component

```astro
---
// FRONTMATTER — runs at build time, on the server, once.
// Node APIs available. Nothing here reaches the browser.
import { getCollection } from 'astro:content';
import Layout from '../layouts/Base.astro';

const events = await getCollection('events');
const featured = events.filter(e => e.data.featured);
---

<!-- TEMPLATE — rendered to static HTML -->
<Layout title="Our work">
  {featured.map(event => (
    <article>
      <h2>{event.data.title}</h2>
    </article>
  ))}
</Layout>

<style>
  /* Scoped to this component automatically */
  article { display: grid; gap: var(--space-s); }
</style>
```

Three rules that follow:

- **`onClick` in an `.astro` template does nothing.** There is no client runtime.
  Use a `<script>` tag or an island.
- **A plain `<script>` in an `.astro` file is bundled and processed**, and runs
  once per page — not per component instance. Use `is:inline` to opt out.
- **Frontmatter cannot access `window`.** If you need it, you need a `<script>`
  or an island.

### Routing

| Path | Route |
|---|---|
| `src/pages/index.astro` | `/` |
| `src/pages/work/index.astro` | `/work/` |
| `src/pages/work/[slug].astro` | `/work/:slug/` |
| `src/pages/[...path].astro` | catch-all |
| `src/pages/404.astro` | not found |

Dynamic routes are enumerated at build time:

```astro
---
import { getCollection, render } from 'astro:content';

export async function getStaticPaths() {
  const events = await getCollection('events');
  return events.map(event => ({
    params: { slug: event.id },
    props: { event },
  }));
}

const { event } = Astro.props;
const { Content } = await render(event);
---
<Content />
```

`getStaticPaths` **must** be exported from the page and cannot access
`Astro.params` — it is what generates them.

### Layouts and slots

```astro
---
// src/layouts/Base.astro
interface Props { title: string; description?: string; }
const { title, description = 'Event organisers in Latur' } = Astro.props;
---
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>{title}</title>
    <meta name="description" content={description}>
    <slot name="head" />
  </head>
  <body>
    <Header />
    <main><slot /></main>
    <Footer />
  </body>
</html>
```

Named slots (`<slot name="head" />`) let a page contribute to the layout's head
without the layout knowing about every page.

### Conventions for this project

- **Static output.** `output: 'static'` unless a specific route needs a server.
  A portfolio's only dynamic need is the enquiry form; see `static-deploy`.
- **No UI framework in `.astro` components.** Islands only.
- **Every image through `astro:assets`.** Never a raw `<img src="/photo.jpg">`.
- **One `<Layout>` per page**, never nested layouts more than two deep.
- **Content lives in collections**, not in page frontmatter.

### Detection

```bash
grep -rn 'onclick=\|onClick=' src/ --include=*.astro          # no client runtime
grep -rn '<img ' src/ --include=*.astro | grep -v 'astro:assets\|Image\|Picture'
grep -rn 'client:' src/components/ --include=*.astro           # islands outside src/islands
find public -name '*.jpg' -o -name '*.png' | head              # unoptimised photos
grep -rn 'window\.\|document\.' src/ --include=*.astro | grep -v '<script'
npx astro check                                                # type + config errors
```

**Verify the build, not the dev server.** Dev is more forgiving:

```bash
npm run build && npx serve dist
grep -c '<script' dist/index.html      # how much JS actually shipped
du -sh dist
```

### Caveats

- **`npm run dev` hides real problems.** Build before believing anything about
  performance or bundle size.
- **Astro 5's Content Layer replaced the pre-v5 collections API.** Guides written
  for Astro 3/4 use `src/content/config.ts` and a different loader model — check
  the version before copying any example.
- **`getStaticPaths` runs once**, at build. A new case study needs a rebuild;
  that is a deploy-hook problem, not a code problem. See `git-cms`.
- **Scoped styles do not cross into islands.** Framework components need their
  own styling approach; see `css-architecture`.
- **Astro's docs are MIT-licensed code with CC BY-NC-SA docs** — quote, don't
  bulk-copy.

### Checklist

- [ ] Project laid out with `assets`/`islands`/`content`/`layouts` separated
- [ ] `output: 'static'` unless a route genuinely needs a server
- [ ] Photographs in `src/assets`, not `public`
- [ ] Every image rendered via `astro:assets`
- [ ] No `onClick` in `.astro` templates
- [ ] `getStaticPaths` exported on every dynamic route
- [ ] Shared chrome in a layout with named slots
- [ ] `astro check` passes
- [ ] Built output inspected — script count and `dist` size
- [ ] No page ships JS it does not need

## References

- **Astro docs — Project structure**
  <https://docs.astro.build/en/basics/project-structure/>
- **Astro docs — Astro components, frontmatter, and the template**
  <https://docs.astro.build/en/basics/astro-components/>
- **Astro docs — Routing and `getStaticPaths`**
  <https://docs.astro.build/en/guides/routing/>
- **Astro docs — Layouts and slots**
  <https://docs.astro.build/en/basics/layouts/>
- **Astro docs — Images**, on `src/assets` versus `public`
  <https://docs.astro.build/en/guides/images/>
- **Astro blog — "Content Layer: A Deep Dive"**, on what changed in v5
  <https://astro.build/blog/content-layer-deep-dive/>

**Not sourced — written for this framework:** the project-conventions list, the
"verify the build not the dev server" rule, the directory split putting islands
in their own folder, and the detection commands.
