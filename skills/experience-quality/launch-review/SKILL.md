---
name: launch-review
version: 1.0.0
description: |
  The gate a portfolio site must pass before going live, and the handover the
  client needs afterwards. Use in the days before launch, and as the final
  sign-off before DNS is pointed.
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
---

## Launch Review

Launch is where every deferred decision comes due at once. This skill is the
consolidated gate — it references the other skills rather than repeating them,
and it is deliberately a **blocking** checklist.

**Run it twice:** once a week before launch, so there is time to fix what it
finds, and once on the day.

> **Before running anything:** confirm who has authority to sign off. A launch
> with no named approver produces a launch nobody approved.

### Method

1. **Run the automated gates** — they are fast and they fail loudly.
2. **Work the manual gate** below, section by section.
3. **Record every result**, including what was not tested.
4. **Get named sign-off** from the client on content and consent.
5. **Do the handover** — the site is not delivered until the client can use it.

### Automated gates

```bash
set -e
npm run build                                  # schemas + types must pass
npx astro check
./bin/check-budget.sh                          # → performance-budget
npx playwright test                            # a11y + overflow → accessibility, cross-device-testing
npx lighthouse https://staging.eventina.in/ --form-factor=mobile --quiet
npx pa11y-ci --sitemap https://staging.eventina.in/sitemap-index.xml
npx linkinator https://staging.eventina.in/ --recurse --silent   # broken links
```

### Content gate

- [ ] Every case study has a real problem section — no thin entries → `case-study-structure`
- [ ] Every published image has consent recorded → `media-consent`
- [ ] **No children's images without verifiable guardian consent**
- [ ] EXIF stripped from every image; no GPS survives → `asset-workflow`
- [ ] Every claim, number, and award verified or removed → `brand-narrative`
- [ ] Venue and family names cleared
- [ ] Testimonials have recorded consent for quote, name, and photo → `testimonial-curation`
- [ ] No placeholder text anywhere

```bash
grep -rniE 'lorem ipsum|TODO|FIXME|placeholder|coming soon|xxx' src/content/ src/pages/
exiftool -GPSLatitude src/assets/events/*/*.jpg 2>/dev/null | grep -i gps && echo "GPS FOUND"
grep -rn 'verified: false\|consent: false' src/content/
```

### Technical gate

- [ ] LCP ≤2.5 s, INP ≤200 ms, CLS ≤0.1 on a throttled mid-range Android → `core-web-vitals`
- [ ] All budgets met → `performance-budget`
- [ ] axe clean on every template → `accessibility`
- [ ] Keyboard-only pass through every flow, including the lightbox
- [ ] Screen-reader pass on home, one case study, and contact
- [ ] No horizontal overflow at 320 px → `cross-device-testing`
- [ ] Instagram in-app browser verified on Android
- [ ] Reduced motion: all content still visible → `motion-design`
- [ ] 404 page designed and reachable
- [ ] Security headers present; CSP tested live → `static-deploy`
- [ ] HTTPS enforced; HSTS set
- [ ] `/admin/` noindex and disallowed → `git-cms`

### Discovery gate

- [ ] `site` set; canonical URLs correct → `static-deploy`
- [ ] Unique `<title>` and meta description per page → `seo-foundations`
- [ ] Structured data validates → `structured-data`
- [ ] Open Graph image renders correctly in a **real WhatsApp message** → `social-sharing`
- [ ] Sitemap submitted to Google Search Console
- [ ] Google Business Profile updated with the new URL → `local-discovery`
- [ ] Old URLs redirected if replacing an existing site
- [ ] Analytics recording, and cookieless

```bash
curl -s https://staging.eventina.in/ | grep -oP '<title>.*?</title>'
curl -s https://staging.eventina.in/ | grep -oP '<meta property="og:[^>]*>'
curl -sI https://eventina.in/ | grep -iE 'strict-transport|content-security|x-content-type'
```

### Enquiry gate

**Test the form end to end on the real domain, and confirm the email arrives.**
This is the one failure that costs the client money directly, and it is the most
common launch-day bug — CSP `form-action` and endpoint allow-lists are
domain-specific and pass on staging.

- [ ] Form submitted on the production domain; email received
- [ ] Works with JavaScript disabled → `static-deploy`
- [ ] WhatsApp link opens the correct number with a prefilled message → `enquiry-conversion`
- [ ] Phone number is a `tel:` link and dials correctly on a phone
- [ ] Thank-you page reachable and correct
- [ ] Spam protection active
- [ ] Client knows where enquiries arrive and has tested receiving one

### Legal and privacy gate

- [ ] Privacy notice published, covering the enquiry form and photographs → `media-consent`
- [ ] Erasure contact published and monitored
- [ ] **Erasure path tested end to end, including CDN purge**
- [ ] Photographer credits honoured where required → `asset-workflow`
- [ ] Client told in writing that this framework is not legal advice

### Handover

The site is not delivered until the client can operate it.

- [ ] Client can log into the CMS and has published one case study **unaided**
- [ ] Client knows the site rebuilds in minutes, not instantly
- [ ] Client understands the consent field and why it blocks publishing
- [ ] Domain, DNS, host, and repository access confirmed in the client's own name
- [ ] Written handover: how to publish, who to call, what not to touch
- [ ] Backup of content and assets confirmed outside the repo

**Access in the client's name is not optional.** A domain registered to the
agency is a hostage situation waiting to happen, however good the relationship
is now.

### Post-launch, first week

- [ ] Search Console: coverage errors, mobile usability
- [ ] Field Core Web Vitals beginning to collect
- [ ] Enquiry emails arriving and being answered
- [ ] Analytics sane — traffic from Instagram appearing as expected
- [ ] 404s in the logs reviewed and redirected

### Caveats

- **A checklist is not a substitute for judgement.** Everything can pass and the
  site can still be wrong for the client.
- **Do not launch on a Friday**, or the day before anyone is unreachable.
- **Staging is not production.** Re-verify headers, forms, and redirects on the
  live domain.
- **Some items will fail and be launched anyway.** That is a legitimate business
  decision — record it as a known issue with an owner and a date, rather than
  quietly ticking the box.

## References

- **`case-study-structure`, `media-consent`, `asset-workflow`, `accessibility`,
  `core-web-vitals`, `performance-budget`, `cross-device-testing`,
  `static-deploy`, `seo-foundations`, `structured-data`, `social-sharing`,
  `local-discovery`, `enquiry-conversion`, `git-cms`** (this framework) — each
  gate above is the summary of one of these
- **web.dev — Core Web Vitals thresholds** <https://web.dev/articles/vitals>
- **WCAG 2.2** <https://www.w3.org/TR/WCAG22/>
- **Google Search Central — Search Console setup and sitemap submission**
  <https://support.google.com/webmasters/answer/9128668>
- **pa11y-ci** <https://github.com/pa11y/pa11y-ci> and **linkinator**
  <https://github.com/JustinBeckwith/linkinator>

**Not sourced — written for this framework:** the gate structure, the
form-on-production warning, the handover requirements including client-name
access, the "record known issues rather than tick the box" rule, and the
first-week list.
