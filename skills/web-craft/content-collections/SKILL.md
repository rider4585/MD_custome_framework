---
name: content-collections
version: 1.0.0
description: |
  Model portfolio content as typed, validated collections so incomplete work
  cannot ship — schemas, image references, relations, and querying. Use when
  defining the content model, adding a content type, or when a page breaks
  because a field was missing.
allowed-tools:
  - Read
  - Write
  - Edit
  - Grep
  - Glob
  - Bash
---

## Content Collections

The content model is the most consequential decision on a portfolio site. It
determines what the CMS can offer an editor, what the pages can render, and —
most valuably — **what the build refuses to accept**.

The framework's position: **use the schema as a quality gate.** A case study with
four photographs and no problem statement should fail the build, not ship thin.
That single idea prevents most portfolio decay.

> **Before running anything:** load `case-study-structure` for what a case study
> must contain. This skill encodes that; it does not decide it.

### Method

1. **List the content types** and how they relate.
2. **Write schemas with real constraints** — minimums, enums, required consent.
3. **Reference images through `image()`** so they get optimised and validated.
4. **Model relations by id**, validated at build.
5. **Query in frontmatter**, never at runtime.

### Collections for this site

| Collection | Holds | Relates to |
|---|---|---|
| `events` | Case studies | `services`, `testimonials` |
| `services` | What the company offers | `events` (examples) |
| `testimonials` | Client quotes | `events` |
| `pages` | About, privacy, contact copy | — |

### Defining a collection

```ts
// src/content.config.ts   (Astro 5 — note: NOT src/content/config.ts)
import { defineCollection, reference, z } from 'astro:content';
import { glob } from 'astro/loaders';

const events = defineCollection({
  loader: glob({ pattern: '**/*.md', base: './src/content/events' }),
  schema: ({ image }) => z.object({
    title:     z.string().min(3).max(60),
    eventType: z.enum(['wedding','sangeet','mehendi','corporate','birthday','civic']),
    emotion:   z.enum(['reverence','exuberance','competence','delight','gravitas']),
    date:      z.coerce.date(),
    location:  z.string(),

    hero:    image(),
    heroAlt: z.string().min(10, 'Write a real description, not a filename'),

    // Quality gates — see case-study-structure
    brief:     z.string().min(150).max(500),
    problem:   z.string().min(150).max(600),
    decisions: z.array(z.string().min(20)).min(3).max(5),

    services:    z.array(reference('services')).min(1),
    testimonial: reference('testimonials').optional(),

    mediaConsent: z.object({
      clientContract: z.string(),
      grantedOn:      z.coerce.date(),
      verified:       z.literal(true),
    }),

    draft:    z.boolean().default(false),
    featured: z.boolean().default(false),
  }),
});

export const collections = { events /* , services, testimonials, pages */ };
```

**Astro 5 puts this at `src/content.config.ts`.** Pre-v5 guides say
`src/content/config.ts` — a very common and confusing failure, because the old
path is silently ignored rather than erroring.

### Images in schemas

The `image()` helper validates that the file exists and returns optimisable
metadata:

```ts
schema: ({ image }) => z.object({
  hero: image(),
  gallery: z.array(z.object({
    src:  image(),
    alt:  z.string().min(10),
    role: z.enum(['establishing','hero','detail','human','scale','closing']),
  })).min(9).max(14),
})
```

- Paths in the markdown are **relative to the markdown file**, so co-locate
  images with content.
- A missing file **fails the build** — no broken images in production.
- `z.string().url()` for remote images instead; they are not optimised unless the
  domain is allowed in `image.domains`.

### Relations

```ts
testimonial: reference('testimonials').optional(),
services:    z.array(reference('services')).min(1),
```

`reference()` validates that the target id exists at build time. Resolve it when
rendering:

```astro
---
import { getEntry, getCollection } from 'astro:content';

const { event } = Astro.props;
const testimonial = event.data.testimonial
  ? await getEntry(event.data.testimonial)
  : undefined;
---
```

### Querying

```astro
---
import { getCollection } from 'astro:content';

// Filter drafts in the same call, not afterwards
const published = await getCollection('events', ({ data }) =>
  import.meta.env.PROD ? !data.draft : true
);

const featured = published
  .filter(e => e.data.featured)
  .sort((a, b) => b.data.date.valueOf() - a.data.date.valueOf())
  .slice(0, 6);

const byType = Object.groupBy(published, e => e.data.eventType);
---
```

**All of this runs at build time.** There is no runtime query and no database —
which is why the site is fast and has nothing to secure.

### Enforcing cross-field rules

Zod's `refine` and `superRefine` catch what per-field types cannot:

```ts
schema: ({ image }) => z.object({ /* ... */ })
  .refine(d => d.gallery.some(g => g.role === 'hero'), {
    message: 'Gallery must contain exactly one image with role "hero"',
    path: ['gallery'],
  })
  .refine(d => d.gallery.filter(g => g.role === 'establishing').length === 1, {
    message: 'Exactly one establishing shot required',
    path: ['gallery'],
  })
```

**Write the error message for the person who will see it** — often a content
editor in the CMS, not a developer.

### Detection

```bash
ls src/content.config.ts 2>/dev/null || echo "MISSING or at the pre-v5 path"
ls src/content/config.ts 2>/dev/null && echo "WRONG PATH for Astro 5"
grep -rn 'z.string()' src/content.config.ts | grep -v 'min(\|max(\|enum\|url'  # unconstrained
grep -rn 'getCollection' src/ --include=*.astro | grep -v '^src/pages\|^src/layouts'
grep -rln 'draft: true' src/content/                       # drafts present
npx astro check && npm run build                            # schemas validate on build
```

### Caveats

- **A schema that is too strict blocks the client.** Every `min()` is a rule
  someone must satisfy at 11pm before a launch. Constrain what protects quality;
  leave the rest optional.
- **Changing a schema breaks existing content.** Migrate the markdown in the same
  commit, or make the field optional with a default.
- **`reference()` failures are cryptic** — they name the id, not the file. Keep
  ids equal to filenames.
- **Collections are build-time only.** A content change requires a rebuild; wire
  the CMS to a deploy hook. See `git-cms`.
- **The CMS config must mirror the schema.** They are two descriptions of one
  model and they drift. See `git-cms`.

### Checklist

- [ ] Content types listed with their relations before any schema is written
- [ ] Config at `src/content.config.ts` (Astro 5 path)
- [ ] Every string field constrained — `min`, `max`, or `enum`
- [ ] Quality gates encode `case-study-structure`'s requirements
- [ ] Images via `image()`, co-located with their markdown
- [ ] Alt text required with a real minimum length
- [ ] Consent fields required and `literal(true)`
- [ ] Relations use `reference()`, not free strings
- [ ] Drafts filtered inside `getCollection`, in production only
- [ ] Cross-field rules via `refine` with editor-readable messages
- [ ] `astro check` and `npm run build` both pass
- [ ] CMS config mirrors the schema

## References

- **Astro docs — Content collections**, `defineCollection`, `glob()` loader, the
  `image()` helper, `reference()`, and the Astro 5 config path
  <https://docs.astro.build/en/guides/content-collections/>
- **Astro blog — "Content Layer: A Deep Dive"**, on the v5 rewrite and why the
  loader model changed <https://astro.build/blog/content-layer-deep-dive/>
- **Zod documentation** — `refine`, `superRefine`, `coerce`, `literal`
  <https://zod.dev/>
- **`case-study-structure`** (this framework) — the requirements these schemas
  encode

**Not sourced — written for this framework:** the collection set for this site,
the schema-as-quality-gate position, the specific minimums, the cross-field
`refine` rules, and the detection commands.
