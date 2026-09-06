# Sources

Every skill and agent in this framework cites its sources inline. This file is
the consolidated bibliography, with **licence** stated so you know what you can
copy, vendor, or ship.

Verified reachable on **2026-09-07**. Where a project's status changed recently,
that is noted — the note is the point, not the link.

> **How to read a citation.** A skill cites *specifically* — "WCAG 2.2 SC 2.3.3
> Animation from Interactions", not "WCAG". If you cannot check the claim from
> the citation given, that is a bug in the skill. Report it.

---

## 1. Standards and specifications

| Source | What it governs here | Licence |
|---|---|---|
| [WCAG 2.2](https://www.w3.org/TR/WCAG22/) — W3C Recommendation | Accessibility floor. Cited by success criterion | W3C Document Licence |
| [WAI-ARIA Authoring Practices Guide (APG)](https://www.w3.org/WAI/ARIA/apg/) | Keyboard and role patterns for galleries, dialogs, carousels | W3C Document Licence |
| [Schema.org](https://schema.org/) — `Event`, `LocalBusiness`, `ImageObject`, `Review`, `FAQPage`, `BreadcrumbList` | Structured data vocabulary | CC BY-SA 3.0 |
| [Open Graph protocol](https://ogp.me/) | Link previews for WhatsApp, Instagram, Facebook | Open Web Foundation Agreement 0.9 |
| [CSS Scroll-driven Animations](https://www.w3.org/TR/scroll-animations-1/) — W3C | Native scroll choreography without JS | W3C Document Licence |
| [View Transitions API](https://www.w3.org/TR/css-view-transitions-1/) — W3C | Cinematic page-to-page transitions | W3C Document Licence |
| [MDN Web Docs](https://developer.mozilla.org/) | Reference for every CSS/HTML/JS API used | CC BY-SA 2.5 |

## 2. Performance

| Source | What it governs here | Licence / access |
|---|---|---|
| [web.dev — Core Web Vitals](https://web.dev/articles/vitals) | LCP / INP / CLS definitions and thresholds | CC BY 4.0 |
| [web.dev — Optimize LCP](https://web.dev/articles/optimize-lcp) | The hero-image problem, which is *the* problem on a photo portfolio | CC BY 4.0 |
| [Chrome UX Report (CrUX)](https://developer.chrome.com/docs/crux) | Field data, as opposed to lab data | CC BY 4.0 |
| [Lighthouse](https://github.com/GoogleChrome/lighthouse) | Lab auditing | Apache-2.0 |
| [WebPageTest](https://www.webpagetest.org/) | Throttled real-device testing | Free tier / paid |

## 3. Media tooling

| Source | Use | Licence |
|---|---|---|
| [sharp](https://github.com/lovell/sharp) | Build-time image transcoding; what Astro uses under the hood | Apache-2.0 |
| [libvips](https://github.com/libvips/libvips) | sharp's engine; the reason it is fast and low-memory | LGPL-2.1 |
| [Squoosh](https://github.com/GoogleChromeLabs/squoosh) | Manual per-image quality tuning for hero art | Apache-2.0 |
| [FFmpeg](https://ffmpeg.org/) | Video transcoding, poster-frame extraction, silent-track stripping | LGPL-2.1 / GPL-2.0 |
| [ExifTool](https://exiftool.org/) | Stripping GPS and camera metadata before publishing | Perl Artistic / GPL |
| [PhotoSwipe](https://photoswipe.com/) | Touch-first lightbox with real keyboard support | MIT |
| [Embla Carousel](https://www.embla-carousel.com/) | Accessible, dependency-free carousel when one is unavoidable | MIT |

## 4. Motion

| Source | Use | Licence |
|---|---|---|
| [Motion](https://motion.dev/) | JS animation where CSS cannot reach. **MIT, irrevocably** | MIT |
| [Lenis](https://github.com/darkroomengineering/lenis) | Smooth scroll, ~4 kB. Use sparingly — it hijacks a native affordance | MIT |
| GSAP + ScrollTrigger — [gsap.com](https://gsap.com/pricing/) | Made **free for commercial use by Webflow in April 2025**, but it is **not open source**: you may not fork or decompile it. Prefer Motion where the licence matters to you | Free, proprietary |
| [MDN — `prefers-reduced-motion`](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion) | The non-negotiable guard on every animation in this framework | CC BY-SA 2.5 |

## 5. Type, colour, and layout systems

| Source | Use | Licence / access |
|---|---|---|
| [Utopia](https://utopia.fyi/) | Fluid type and space scales via `clamp()` — two poles, browser interpolates | Free tool, CSS output is yours |
| [Open Props](https://open-props.style/) | Ready-made CSS custom properties for easings, shadows, colour | MIT |
| [Modern Font Stacks](https://modernfontstacks.com/) | System-font stacks that cost zero bytes | MIT |
| [Fontsource](https://fontsource.org/) | Self-hosting open fonts; no third-party request, no privacy leak | MIT (fonts keep their own licences) |
| [Butterick's *Practical Typography*](https://practicaltypography.com/) | Measure, line height, restraint | Free to read |
| [Tiro Devanagari Marathi](https://fonts.google.com/specimen/Tiro+Devanagari+Marathi) | Marathi display/serif — designed for Marathi specifically, with an italic | SIL OFL 1.1 |
| [Mukta](https://fonts.google.com/specimen/Mukta) | Marathi UI sans, 7 weights, humanist | SIL OFL 1.1 |
| [Noto Sans Devanagari](https://fonts.google.com/noto/specimen/Noto+Sans+Devanagari) | Fallback with the widest glyph coverage | SIL OFL 1.1 |

## 6. Build, content, and deploy

| Source | Use | Licence |
|---|---|---|
| [Astro docs — Images](https://docs.astro.build/en/guides/images/) | `<Image>`, `<Picture>`, `getImage()`, `image()` schema helper | MIT (docs CC BY-NC-SA 4.0) |
| [Astro docs — Content collections](https://docs.astro.build/en/guides/content-collections/) | Content Layer API (Astro 5), typed schemas | MIT |
| [Astro — Content Layer deep dive](https://astro.build/blog/content-layer-deep-dive/) | Why collections were rewritten in v5 | Blog |
| [Sveltia CMS](https://github.com/sveltia/sveltia-cms) | Git-based CMS. **Actively maintained; reads Decap/Netlify CMS config as-is** | MIT |
| [Decap CMS](https://decapcms.org/) | The predecessor. Config format is the de-facto standard; **maintenance has been intermittent** — cited for its config schema, not recommended as the runtime | MIT |
| [Pagefind](https://pagefind.app/) | Static-site search that needs no server | MIT |
| [Umami](https://github.com/umami-software/umami) / [Plausible](https://github.com/plausible/analytics) | Cookieless analytics — avoids a consent banner entirely | MIT / AGPL-3.0 |

## 7. Craft and research writing

| Source | Use | Access |
|---|---|---|
| [Inclusive Components](https://inclusive-components.design/) — Heydon Pickering | Galleries, menus, dialogs done accessibly | Free to read |
| [The A11Y Project](https://www.a11yproject.com/) | Practical checklist | CC0 / open source |
| [axe-core](https://github.com/dequelabs/axe-core) | Automated accessibility rules engine | MPL-2.0 |
| [Playwright](https://playwright.dev/) | Cross-device and visual regression testing | Apache-2.0 |
| [Nielsen Norman Group](https://www.nngroup.com/articles/) | Scanning patterns, trust, form usability | Free articles |
| [Smashing Magazine](https://www.smashingmagazine.com/) | Long-form technique articles, cited per-article | Free |
| [Google Search Central](https://developers.google.com/search/docs) | Crawling, indexing, structured-data eligibility | CC BY 4.0 |
| [Google Business Profile Help](https://support.google.com/business/) | Local discovery — the dominant channel for a Latur event company | Free |

## 8. Legal and consent (India)

| Source | Use | Access |
|---|---|---|
| [Digital Personal Data Protection Act, 2023](https://www.meity.gov.in/data-protection-framework) — MeitY | Consent, purpose limitation, and erasure for guest photographs | Government of India |
| [DPDP Rules, 2025 — PIB press note](https://www.pib.gov.in/PressNoteDetails.aspx?NoteId=156054&ModuleId=3&reg=3&lang=2) | **Notified 14 November 2025**, with an eighteen-month phased compliance window. This is what makes photo consent an engineering requirement, not a nicety | Government of India |

> **This framework is not legal advice.** The DPDP citations exist so an agent
> raises the question and records the client's answer, not so it decides the law.
> Anything with legal consequence goes to the client with a named human decision.

## 9. Business facts — Eventina Organisers

Gathered on 2026-09-07 from public listings. **Marked `[to verify]` where a
secondary source is the only source** — see `skills/project-knowledge/`.

| Fact | Source |
|---|---|
| Latur, Maharashtra; Kailash Plaza, beside Manas Hotel, Barshi Road, Ganj Golai, 413512 | [Justdial listing](https://www.justdial.com/Latur/Eventina-Organisers-Near-Manas-Hotel-Latur-HO/9999P2382-2382-191109130517-W8Y2_BZDET) `[to verify]` |
| Established 2016; weddings, birthdays, dance parties | Justdial listing `[to verify]` |
| 4.8 / 5 from 93 ratings | Justdial listing `[to verify]` |
| Instagram `@eventina.organisers` — "Eventina 2.0 ✨ New account. Same passion." | [Instagram](https://www.instagram.com/eventina.organisers/) |
| Prior Instagram account `@eventinaorganisers` | [Instagram](https://www.instagram.com/eventinaorganisers/) |
| Facebook page `Eventina.in` | [Facebook](https://www.facebook.com/Eventina.in/) |
| Registered style "Eventina Organisers Pvt Ltd" | Instagram page title `[to verify]` |

**The Instagram grid itself could not be read** — Instagram serves a login wall to
automated fetches. Post captions, image count, event mix, and the current bio are
therefore **unknown**, and `project-content-inventory` treats them as a blocking
gap to be filled by a human with account access.

---

**Not sourced — written for this framework:** the emotion-per-event-type model,
the case-study anatomy, the media-consent workflow, the enquiry-conversion
patterns for a WhatsApp-first market, all agent objectives, and every checklist.
Those are this framework's opinions. They are argued for in the skills that
contain them, and they are the parts you should push back on first.
