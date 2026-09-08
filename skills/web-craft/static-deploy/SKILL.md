---
name: static-deploy
version: 1.0.0
description: |
  Get a static portfolio online reliably — build pipeline, caching headers,
  security headers, the enquiry-form endpoint a static site cannot provide
  itself, redirects, and analytics. Use when setting up hosting, when the form
  does not work, or before any launch.
allowed-tools:
  - Read
  - Write
  - Edit
  - Grep
  - Glob
  - Bash
---

## Static Deploy

A static site has almost no attack surface and almost no running cost. The two
things it cannot do by itself are **receive a form submission** and **run
anything on request** — and both come up on a portfolio.

This skill covers everything between `npm run build` and a working public site.

> **Before running anything:** confirm who owns the domain and the DNS. Launch
> delays are more often a registrar access problem than a technical one — check
> weeks ahead, not on launch day.

### Method

1. **Choose a host** and wire build-on-push.
2. **Set caching headers** — hashed assets immutable, HTML never.
3. **Set security headers.**
4. **Pick a form endpoint** and make it work without JavaScript.
5. **Configure redirects, `robots.txt`, and a sitemap.**
6. **Run the launch checklist** before pointing DNS.

### Hosting

| Host | Notes |
|---|---|
| **Cloudflare Pages** | Generous free tier, good India edge presence, Functions for the form endpoint |
| **Netlify** | Simplest forms story (built-in), good DX |
| **Vercel** | Fine; strongest for SSR, which this site does not need |
| **GitHub Pages** | Free and simple, but no headers control and no functions — **not sufficient here** |

**India edge presence matters** for this audience. Verify actual TTFB from the
target region rather than trusting a marketing map — see `core-web-vitals`.

### Caching

The single highest-value configuration, and routinely missed:

```
# public/_headers  (Cloudflare Pages / Netlify)

/_astro/*
  Cache-Control: public, max-age=31536000, immutable

/fonts/*
  Cache-Control: public, max-age=31536000, immutable

/*.html
  Cache-Control: public, max-age=0, must-revalidate

/
  Cache-Control: public, max-age=0, must-revalidate
```

**Hashed assets are immutable; HTML never is.** Astro fingerprints everything in
`_astro/`, so those URLs can be cached for a year — the filename changes when the
content does. Caching HTML, by contrast, means a published case study does not
appear for the client, who then republishes it three times.

### Security headers

Small surface, but free to secure:

```
/*
  X-Content-Type-Options: nosniff
  Referrer-Policy: strict-origin-when-cross-origin
  Strict-Transport-Security: max-age=31536000; includeSubDomains
  Permissions-Policy: geolocation=(), microphone=(), camera=(), interest-cohort=()
  Content-Security-Policy: default-src 'self'; img-src 'self' data:; font-src 'self'; style-src 'self' 'unsafe-inline'; script-src 'self'; frame-ancestors 'none'; form-action 'self' https://your-form-endpoint.example.com
```

- **`frame-ancestors 'none'`** prevents clickjacking and supersedes
  `X-Frame-Options`.
- **`style-src 'unsafe-inline'`** is needed for Astro's scoped styles and inline
  `view-transition-name`. Tighten with hashes if the client requires it.
- **Add the form endpoint to `form-action`** or submissions are blocked — an easy
  bug to ship, because it only manifests on a real submission.
- **Test the CSP before launch.** Deploy with `Content-Security-Policy-Report-Only`
  first and read the console.

### The enquiry form

A static site cannot receive a POST. Options:

| Option | Cost | Notes |
|---|---|---|
| **Netlify Forms** | Free tier | Zero code: add `data-netlify="true"`. Only on Netlify |
| **Cloudflare Pages Function** | Free tier | ~20 lines; full control; forwards to email or WhatsApp API |
| **Formspree / Web3Forms** | Free tier | Third party sees enquiry data — a DPDP consideration; see `media-consent` |
| **Mailto link** | Free | Not a form. Loses most enquiries |

**Make the form work without JavaScript.** A native `<form method="post">` that
posts to the endpoint and redirects to a thank-you page works everywhere; enhance
with `fetch` for an inline success message.

```js
// functions/api/enquiry.js — Cloudflare Pages Function
export async function onRequestPost({ request, env }) {
  const form = await request.formData();

  if (form.get('company')) return Response.redirect('/thank-you/', 303); // honeypot

  const name = String(form.get('name') ?? '').trim();
  const phone = String(form.get('phone') ?? '').trim();
  if (!name || !phone) {
    return Response.redirect('/contact/?error=missing', 303);
  }

  await fetch('https://api.resend.com/emails', {
    method: 'POST',
    headers: {
      Authorization: `Bearer ${env.RESEND_KEY}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      from: 'site@studio.example',
      to: env.ENQUIRY_TO,
      subject: `Enquiry — ${name}`,
      text: [...form.entries()].map(([k, v]) => `${k}: ${v}`).join('\n'),
    }),
  });

  return Response.redirect('/thank-you/', 303);
}
```

- **A honeypot field beats a CAPTCHA** for this volume — no accessibility cost,
  no third-party script, no user friction.
- **Secrets in environment variables**, never in the repo.
- **Rate-limit at the edge** if spam appears.
- **Reply-to expectations belong in the copy** — see `web-copywriting`.
- **Say what you do with the data.** The privacy notice must cover the enquiry
  form; see `media-consent`.

### Redirects, sitemap, robots

```
# public/_redirects
/work/*    /work/:splat/    301
/gallery   /work/           301
/index.html  /              301
```

Pick trailing-slash behaviour and enforce it — inconsistency creates duplicate
URLs and splits any ranking signal.

```js
// astro.config.mjs
import sitemap from '@astrojs/sitemap';
export default defineConfig({
  site: 'https://studio.example',        // required, or the sitemap has no absolute URLs
  integrations: [sitemap()],
  trailingSlash: 'always',
});
```

```
# public/robots.txt
User-agent: *
Allow: /
Disallow: /admin/
Sitemap: https://studio.example/sitemap-index.xml
```

**`site` must be set** or canonical URLs, the sitemap, and Open Graph URLs are
all wrong. See `seo-foundations`.

### Analytics

Prefer **cookieless** analytics — [Umami](https://github.com/umami-software/umami)
(MIT) or [Plausible](https://github.com/plausible/analytics) (AGPL-3.0). No
cookie banner needed, far less personal data to account for under DPDP, and a
fraction of the weight of a tag manager.

If the client insists on Google Analytics, a consent banner comes with it — and
that banner will damage the first impression of a premium site. Say so before
they decide.

### Detection

```bash
grep -n 'site:' astro.config.mjs || echo "MISSING site — canonicals will be wrong"
ls public/_headers public/_redirects public/robots.txt 2>/dev/null
grep -rn 'API_KEY\|SECRET\|password' --include=*.js --include=*.mjs . \
  --exclude-dir=node_modules | grep -v 'env\.'
npm run build && test -f dist/sitemap-index.xml && echo "✓ sitemap"
grep -rn '<form' src/ --include=*.astro | grep -v 'method='   # JS-only form
npx serve dist                                                 # verify the built output

# After deploy
curl -sI https://studio.example/ | grep -i 'cache-control\|content-security\|strict-transport'
curl -sI https://studio.example/_astro/ | grep -i cache-control
```

### Caveats

- **Build minutes are finite on free tiers.** Hundreds of AVIF variants can be
  slow; cache the image transforms.
- **DNS propagation is not instant.** Lower the TTL a day ahead of a cutover.
- **A CDN caches images independently of pages.** After a consent withdrawal, the
  image URL must be purged explicitly — see `media-consent`.
- **Test the form on the real domain.** Endpoint allow-lists and CSP `form-action`
  are domain-specific and pass locally while failing in production.
- **Do not launch on a Friday.**

### Checklist

- [ ] Host chosen with real India edge performance verified
- [ ] Build-on-push wired to `main`
- [ ] `site` set in the Astro config
- [ ] Hashed assets `immutable`; HTML `must-revalidate`
- [ ] Security headers set, including `frame-ancestors 'none'`
- [ ] CSP tested in report-only mode; `form-action` includes the endpoint
- [ ] Form works with JavaScript disabled
- [ ] Honeypot present; no CAPTCHA
- [ ] Secrets in environment variables only
- [ ] Privacy notice covers the enquiry form
- [ ] Redirects and trailing-slash policy consistent
- [ ] `robots.txt` and sitemap present; `/admin/` disallowed
- [ ] Cookieless analytics, or a consent banner properly implemented
- [ ] 404 page designed
- [ ] Headers verified with `curl` against the live domain
- [ ] Form submitted end to end on the real domain
- [ ] DNS TTL lowered ahead of cutover

## References

- **Astro docs — Deploy an Astro site**, per-host build settings
  <https://docs.astro.build/en/guides/deploy/>
- **Astro docs — `@astrojs/sitemap` and the `site` option**
  <https://docs.astro.build/en/guides/integrations-guide/sitemap/>
- **Cloudflare Pages — `_headers`, `_redirects`, and Functions**
  <https://developers.cloudflare.com/pages/configuration/headers/>
- **MDN — HTTP caching, `Cache-Control`, and immutable responses**
  <https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Caching>
- **MDN — Content Security Policy**, including `frame-ancestors` and
  `form-action` <https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CSP>
- **OWASP Secure Headers Project** — the header set above
  <https://owasp.org/www-project-secure-headers/>
- **Google Search Central — `robots.txt` and sitemaps**
  <https://developers.google.com/search/docs/crawling-indexing/robots/intro>
- **Umami** (MIT) / **Plausible** (AGPL-3.0) — cookieless analytics
  <https://github.com/umami-software/umami>

**Not sourced — written for this framework:** the host comparison, the specific
`_headers` values, the Pages Function example, the honeypot-over-CAPTCHA
position, the cookieless-analytics recommendation and its first-impression
argument, and the detection commands.
