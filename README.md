# munder-difflin-agents — `MD_creative_image_photography`

An agent and skill framework for the
[Munder Difflin](https://github.com/chaitanyagiri/munder-difflin) multi-agent
harness, built for **high-end photography portfolio websites** — and configured
for a **wedding and function photographer and videographer** in Latur,
Maharashtra ([`@creative_weddings_films_latur`](https://www.instagram.com/creative_weddings_films_latur/)).

**45 skills · 10 agents · one orchestrator playbook**

---

## What this is

A specialised team for building a portfolio site whose job is to make **the right
couples enquire** — couples who want *this* photographer, not *a* photographer.
Not a generic web-build framework: the skills are written for the specific
problems of a photographer's site — finding the signature hidden in four thousand
frames and culling to twelve, keeping a grid's colour grade consistent,
presenting a wedding film that only works with sound on a page nobody will
unmute, clearing the music before it earns a takedown, publishing photographs of
wedding guests lawfully, and turning a beautiful page into a booked date.

### Branch model

| Branch | Domain |
|---|---|
| `main` | Project README only |
| `MD_IMPOC` | Retail / point-of-sale — 170 skills, 23 agents |
| `MD_eventina` | Event-organiser portfolio — 38 skills, 8 agents |
| **`MD_creative_image_photography`** | **Photography & film portfolio — this branch** |

Branched from `MD_eventina`, which shares the photograph-led portfolio problem.
The creative, web-craft, media, discovery, and quality skills carried across and
were retuned; the four Eventina project-knowledge skills were replaced, and the
**seven `photography-craft` skills and two agents are new** — they cover what a
photographer's site needs and an event organiser's does not: the signature, the
day's narrative beats, films and their music licences, delivery galleries,
packages, image rights, and the vendor referral network.

## ⚠️ Formats are a snapshot, not a contract

Every harness-facing format here — roster entries, spawn requests, skill
locations, message verbs — was verified against **Munder Difflin v0.4.5** on
**2026-09-06** by reading a live install.

**Munder Difflin is under active development.** The harness ships its own
`PROTOCOL.md` and `COMMANDS.md` in the hive root, and **those update with the
app** — where they disagree with anything here, the harness wins. The right
response to drift is to **translate, not force**: these 38 skills are documents,
and their content is independent of how the harness packages them.

Two constraints that shaped the repository, both discovered from a live install:

1. **`identity.md` is written by the harness and is read-only.** You cannot
   hand-author an agent's persona. An agent's expertise comes from **the skills
   installed into its `.claude/skills/`** plus **the `objective` given at spawn
   time**. That is why this repo is mostly skills.
2. **Skills are Claude Code's native `SKILL.md`, in a flat namespace** —
   `agents/<id>/.claude/skills/<name>/SKILL.md`. Names must be globally unique.

## Layout

```
├── SOURCES.md                        ← consolidated bibliography, with licences
├── GOD-PLAYBOOK.md                   ← the creative director reads this each session
├── QUICKSTART.md                     ← human setup guide
├── agents/<category>/*.md            ← 10 agents: roster entry, skills, objective
├── skills/<category>/<name>/SKILL.md ← 45 skills
├── templates/                        ← agent and skill templates
└── bin/install-skills.sh             ← installs skills into an agent
```

## The team

| Category | Agent | Owns |
|---|---|---|
| **Management** | `creative-director` | The signature, the emotional brief, the quality bar, the sequence |
| **Creative** | `brand-strategist` | Positioning, story spine, voice |
| | `art-director` | Image sequencing, layout, type, colour, motion |
| | `cinematographer` | **New** — the film ladder, playback, sound, music licensing |
| | `content-writer` | Interviews, case studies, microcopy, alt text |
| **Engineering** | `astro-engineer` | The Astro build, content model, CSS, CMS, deploy |
| | `media-engineer` | Ingest, EXIF, rights metadata, transcoding, consent records |
| **Quality** | `experience-qa` | Accessibility, Core Web Vitals, devices, launch gate |
| **Growth** | `discovery-specialist` | Structured data, local listings, venue pages, sharing |
| | `client-experience-lead` | **New** — pricing, process, enquiry, delivery, referrals |

## The skills

| Category | # | Covers |
|---|---|---|
| `photography-craft` | **7** | **Signature, story arc, films, delivery, packages, rights, referrals** |
| `creative-direction` | 7 | Emotion mapping, art direction, layout, type, colour, motion |
| `web-craft` | 7 | Astro, content collections, islands, CMS, transitions, CSS, deploy |
| `media-pipeline` | 6 | Optimisation, curation, video, galleries, consent, asset workflow |
| `experience-quality` | 5 | Accessibility, Core Web Vitals, budgets, devices, launch |
| `discovery` | 5 | SEO, structured data, local listings, sharing, enquiry conversion |
| `content-story` | 4 | Case studies, copywriting, testimonials, multilingual |
| `project-knowledge` | 4 | The studio's verified facts, catalogue, inventory, architecture |

`./bin/install-skills.sh --list` prints the full index.

## Conventions

Every skill and agent file:

- **Cites its sources specifically** — "WCAG 2.2 SC 2.3.3 Animation from
  Interactions", not "WCAG"
- **Ends with a "Not sourced — written for this framework" note** naming what is
  original

That second convention is the important one. It lets you tell established
practice from this framework's opinions, and nothing here asks to be taken on
trust without a citation. **[`SOURCES.md`](SOURCES.md)** is the consolidated
bibliography with licences.

Skills are written **stack-agnostically** where the principle outlives the tool,
with the current stack as the worked example.

## Current stack

**Astro** (static output) · content collections with Zod schemas · **Sveltia CMS**
(git-backed, MIT) · self-hosted open-licence fonts · sharp/libvips at build time ·
Playwright + axe-core for verification · cookieless analytics.

Two research findings shaped these choices, both verified 2026-09-07:

- **Decap CMS maintenance has been intermittent.**
  [Sveltia CMS](https://github.com/sveltia/sveltia-cms) is actively developed and
  reads Decap config as-is, so it is the recommended runtime with the config
  format kept portable.
- **GSAP became free for commercial use in April 2025 but is not open source.**
  Since open-source resources were a requirement, native CSS scroll-driven
  animations and [Motion](https://motion.dev/) (MIT) lead the motion guidance.

## The rules that recur

These appear throughout because they are what a portfolio site gets wrong
expensively:

- **A portfolio without a signature is a competence display**, and a competence
  display competes only on price
- **Selection is the premium signal.** Eight weddings shown properly beats forty
  in a grid
- **The frame that gets a photographer hired is the aftermath frame** — the one
  taken ten seconds after the moment every guest also photographed
- **A portfolio cannot out-design its photography.** If the archive has no
  consistent look, report it — no grid or typeface creates one
- **No problem, no case study.** Without a difficulty and a decision it is a
  gallery — publish it as one
- **Consent is a build gate, not a courtesy.** India's DPDP Rules were notified
  in November 2025
- **Music licensing is a build gate too.** A commercial track on a published
  wedding film is commercial use, and crediting the artist has no legal effect
- **Abundance is not readiness.** Eighty thousand frames and zero publishable
  case studies is the normal starting position
- **Delivery galleries are a separate system** from the marketing site, on a
  separate host, `noindex`, never in the sitemap
- **The images are the design.** Layout, type, and colour exist to not damage
  them
- **Motion must reveal structure or relationship**, and every animation ships
  with its reduced-motion variant
- **The LCP image is never lazy-loaded**
- **The agent that writes a fix never verifies it**
- **The audience is a mid-range Android on mobile data**, often inside the
  Instagram in-app browser

## ⛔ Phase 0 is blocking

**Exactly one fact about this client is verified: the Instagram handle.**
Everything else in
[`project-photographer-brand`](skills/project-knowledge/project-photographer-brand/SKILL.md)
is an inference or a question. The trading name, the location, and even whether
film is sold as a product are **inferences from a username** — the grid could not
be read, because Instagram serves a login wall to automated fetches, and a web
search on 2026-09-08 found no directory listing corroborating the business.

**Nothing marked `[to verify]` may be published.** Not in copy, not in structured
data, not in a page title, and above all not in a price. An agent that needs an
unverified fact omits it, marks the gap, and escalates.

This is deliberate and it is the rule most likely to be argued with. A wrong
trading name reaches every page title, the `LocalBusiness` markup, and the
copyright line embedded in every published image.

## Getting started

See **[QUICKSTART.md](QUICKSTART.md)**. The short version:

```bash
./bin/install-skills.sh --agents          # what's in your hive
./bin/install-skills.sh --list            # what's available
./bin/install-skills.sh creative-director signature-style emotional-brief
```

Then have the creative director read [`GOD-PLAYBOOK.md`](GOD-PLAYBOOK.md).

**Before anything works:** enable `orchestratorMaySpawn` in
Settings → Autonomy & Budgets. It is off by default, and without it the
orchestrator cannot spawn.

## Licence

Private. Not for redistribution. Third-party resources keep their own licences —
see [`SOURCES.md`](SOURCES.md).
