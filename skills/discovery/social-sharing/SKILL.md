---
name: social-sharing
version: 1.0.0
description: |
  Make links look right when shared — Open Graph images and metadata for
  WhatsApp, Instagram, and Facebook, plus the Instagram-to-site funnel. Use when
  a shared link renders badly, before launch, or when planning how Instagram
  traffic lands.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## Social Sharing

For this business, **a link is shared far more often than it is searched for.**
A family sends the site to relatives on WhatsApp; the organiser puts it in an
Instagram bio. If that link renders as a bare URL with no image, it is a wasted
impression at the exact moment of a personal recommendation.

**WhatsApp is the primary target here**, not Twitter/X. It has specific and
unforgiving requirements.

> **Before running anything:** confirm `site` is set in the Astro config, or
> every OG URL will be relative and no platform will resolve the image →
> `static-deploy`.

### Method

1. **Set the required tags** on every page, per page.
2. **Generate a real OG image** per case study.
3. **Test in an actual WhatsApp message** — nothing else is proof.
4. **Design the Instagram landing experience.**
5. **Re-scrape after changes**, because previews are cached hard.

### The tags

```astro
---
interface Props { title: string; description: string; image?: ImageMetadata; type?: string; }
const { title, description, image, type = 'website' } = Astro.props;

const canonical = new URL(Astro.url.pathname, Astro.site);
const ogImage = new URL(image?.src ?? '/og-default.jpg', Astro.site);
---
<title>{title}</title>
<meta name="description" content={description} />
<link rel="canonical" href={canonical} />

<meta property="og:type" content={type} />
<meta property="og:url" content={canonical} />
<meta property="og:title" content={title} />
<meta property="og:description" content={description} />
<meta property="og:image" content={ogImage} />
<meta property="og:image:width" content="1200" />
<meta property="og:image:height" content="630" />
<meta property="og:image:alt" content={description} />
<meta property="og:site_name" content="the studio" />
<meta property="og:locale" content="en_IN" />

<meta name="twitter:card" content="summary_large_image" />
<meta name="twitter:title" content={title} />
<meta name="twitter:description" content={description} />
<meta name="twitter:image" content={ogImage} />
```

**`og:image` must be an absolute URL.** Relative paths are the single most common
reason a preview shows no image, and it works locally, so it ships.

**`og:image:width` and `og:image:height` are not optional for WhatsApp.**
Without them, WhatsApp often falls back to a small thumbnail rather than the
large card.

### The image

| Requirement | Value |
|---|---|
| Dimensions | **1200 × 630** (1.91:1) |
| Format | JPEG or PNG. **Not WebP or AVIF** — support is inconsistent across scrapers |
| File size | **Under 300 kB.** WhatsApp is stricter than the others; under 300 kB is the safe target |
| Text in image | Large, centred, high contrast — it renders small |
| Per page | A distinct image per case study; one default for everything else |

**The format restriction is easy to get wrong** on a site where everything else
is AVIF. Generate OG images as JPEG explicitly.

```astro
---
// Generate a JPEG OG image from the case study hero
import { getImage } from 'astro:assets';

const og = await getImage({
  src: event.data.hero,
  width: 1200, height: 630, format: 'jpeg', quality: 80, fit: 'cover',
});
---
<meta property="og:image" content={new URL(og.src, Astro.site)} />
```

For a composed card with a title over the image, `@vercel/og` or `satori`
renders one at build time — worthwhile if the client wants branded previews, and
skippable otherwise. **A well-cropped photograph is usually better than a card
with text over it**, because the photograph is the product.

### Testing

**Test in a real WhatsApp message to yourself.** Validators disagree with each
other and with reality.

| Tool | What it checks |
|---|---|
| A WhatsApp message to yourself | **The one that counts** |
| [Facebook Sharing Debugger](https://developers.facebook.com/tools/debug/) | OG parsing; forces a re-scrape |
| [opengraph.xyz](https://www.opengraph.xyz/) | Quick multi-platform preview |

```bash
# Verify the tags on the live page
curl -s https://studio.example/work/sharma-wedding-2026/ \
  | grep -oP '<meta (property|name)="(og|twitter):[^>]*>'

# Confirm the image resolves, and its size
og=$(curl -s https://studio.example/ | grep -oP '(?<=og:image" content=")[^"]+')
curl -sI "$og" | grep -iE 'content-type|content-length'
```

**Previews are cached aggressively.** After changing an image, run the Facebook
debugger to force a re-scrape; WhatsApp's cache generally follows. Otherwise the
client will keep seeing the old preview and report it as broken.

### The Instagram funnel

`@thestudio` is the strongest existing channel. The link in the bio is
where it lands.

- **Send it to a page designed for that arrival**, not necessarily the home page.
  A "start here" or the work index often converts better.
- **Tag the link with a UTM** so it is attributable → `local-discovery`.
- **That traffic arrives in the Instagram in-app browser.** Test there
  specifically → `cross-device-testing`.
- **The first screen must load fast on mobile data.** This audience arrives
  from a feed and leaves quickly → `core-web-vitals`.
- **Do not use a link-in-bio aggregator** if a single destination will do — it
  adds a hop, a third party, and a slower first impression.

### Caveats

- **You cannot control how every platform renders.** Aim for correct tags and a
  strong image; accept variation.
- **WhatsApp is the strictest** on size and format. Optimise for it and the rest
  follow.
- **No default image is worse than a plain one.** Always ship an `og-default.jpg`.
- **OG images bypass the optimisation pipeline** deliberately — they must be
  JPEG. Do not "fix" this by converting them to AVIF.
- **Instagram in-app browser is not Chrome**, even on Android. Test it.

### Checklist

- [ ] `site` set; all OG URLs absolute
- [ ] `og:title`, `og:description`, `og:image`, `og:url`, `og:type` on every page
- [ ] `og:image:width` and `og:image:height` declared
- [ ] `og:image:alt` present
- [ ] `og:locale` set
- [ ] Twitter card tags present
- [ ] OG image 1200 × 630, JPEG or PNG, under 300 kB
- [ ] A distinct OG image per case study
- [ ] `og-default.jpg` shipped for everything else
- [ ] Tested in a real WhatsApp message
- [ ] Facebook Sharing Debugger run to force a re-scrape after changes
- [ ] Instagram bio link updated with a UTM on launch day
- [ ] Landing page for Instagram traffic chosen deliberately
- [ ] Tested inside the Instagram in-app browser

## References

- **The Open Graph protocol** — required properties, `og:image` structured
  properties, `og:locale` <https://ogp.me/>
- **Facebook — Sharing best practices and the Sharing Debugger**, including
  image dimension and re-scrape behaviour
  <https://developers.facebook.com/docs/sharing/webmasters/>
- **Astro docs — `getImage()`** for generating a JPEG OG image at build time
  <https://docs.astro.build/en/guides/images/#generating-images-with-getimage>
- **`cross-device-testing`, `core-web-vitals`, `local-discovery`** (this
  framework) — the Instagram arrival path

**Not sourced — written for this framework:** the WhatsApp-first prioritisation
and its size/format targets, the "photograph beats a text card" position, the
Instagram funnel guidance, and the verification commands.
