# MD_custome_framework

A production-grade agent and skill framework for the
[Munder Difflin](https://github.com/chaitanyagiri/munder-difflin) multi-agent
harness.

Munder Difflin wraps CLI coding agents (Claude Code, Codex, and others) and runs
them as a coordinating team. It ships with generic preconfigured agents. This
project replaces them with a **specialised, opinionated team** — agents that know
a stack and a domain, each carrying a curated set of skills.

---

## Branches

The framework is **branched per domain**. Each branch is a complete, installable
framework; they share conventions and tooling, not content.

| Branch | Skills | Agents | Domain | Version |
|---|---|---|---|---|
| **`main`** | — | — | This README only — project information | — |
| **[`MD_generic`](../../tree/MD_generic)** ⭐ | **219** | **33** | **Domain-neutral. Learns your project by asking** | v2.0.0 |
| [`MD_IMPOC`](../../tree/MD_IMPOC) | 170 | 23 | Retail inventory + point-of-sale system | v1.0.0 |
| [`MD_eventina`](../../tree/MD_eventina) | 38 | 8 | Event-organiser portfolio site | v1.0.0 |
| [`MD_creative_image_photography`](../../tree/MD_creative_image_photography) | 45 | 10 | Wedding photography & film portfolio | v1.1.0 |

### ⭐ Start here: `MD_generic`

**New projects should branch from `MD_generic`.** It merges the other two
lineages into a domain-neutral superset and adds the machinery that specialises
it for a project — an intake interview the orchestrator runs *with you* before
any work begins.

```
1. Install          the orchestrator installs the onboarding pack into itself
2. INTERVIEW        it asks YOU: what is this, for whom, what domain, what is
                    explicitly not in scope                    ← the whole point
3. Personalise      it selects skill packs, fills the project-knowledge files,
                    rewrites the worked examples into your domain, and retunes
                    every agent objective
4. Phase 0          discovery agents document what already exists
5. Work             everything else
```

Steps 2 and 3 are **blocking**. The governing rule is **ask, do not infer**: an
orchestrator that reads the repository and guesses the domain will be plausibly
wrong, and every agent downstream inherits the error without ever seeing the
assumption that produced it. A repository tells you what was built; it does not
tell you what it is *for*.

The client branches remain useful as **worked examples of what step 3 produces** —
what a fully specialised framework ends up looking like.

---

## What each branch contains

```
├── BOOTSTRAP.md            the orchestrator's install + onboarding sequence
├── GOD-PLAYBOOK.md         standing operating procedure, routing, escalation
├── PACKS.md                which skills exist for which project  (MD_generic)
├── QUICKSTART.md           human setup guide
├── SOURCES.md              consolidated bibliography, with licences
├── agents/<category>/*.md  roster entry, curated skill list, spawn objective
├── skills/<cat>/<name>/SKILL.md   Claude Code native SKILL.md files
├── templates/              formats for adding a skill or an agent
└── bin/install-skills.sh   installs skills into an agent's .claude/skills/
```

---

## The branches in detail

### `MD_generic` — domain-neutral, v2.0.0

**219 skills · 33 agents · 23 packs**

The superset, with every project-specific fact stripped out and replaced by the
machinery for asking.

**The onboarding pack** (orchestrator only) is what distinguishes it:

| Skill | Does |
|---|---|
| `project-discovery` | The six-section intake interview — what/why including **non-goals**, who, domain, technical, the work, constraints. Batched into one message; `unknown` recorded rather than guessed |
| `framework-personalisation` | Four passes: select packs → fill project-knowledge → **re-example** the worked examples into your domain → retune agent objectives |
| `agent-roster-design` | Which of the 33 agents this project needs; the volume / separation / distinct-context tests, and when a role should be a skill instead |

Output lands in **`project-context`** — the root file every agent reads first,
every session. An unfilled `project-context` is a blocking state.

**Skills are organised into three tiers** → `PACKS.md`:

| Tier | Packs |
|---|---|
| **Core** — domain-neutral | `planning`, `architecture`, `security`, `qa-testing`, `performance`, `uiux`, `design-system`, `experience-quality`, `content-story`, `creative-direction`, `discovery`, `media-pipeline`, `project-knowledge` |
| **Stack** — install if used | `nodejs-backend`, `react-frontend`, `postgresql`, `web-craft` |
| **Vertical** — install if the domain matches | `retail`, `photography-craft`, `business-analytics`, `customer-intelligence`, `marketing` |

**Lean beats complete.** Every installed skill costs context on every session;
the target is twelve skills per agent. Thirty is a problem, and nothing errors to
tell you — the work just gets quietly worse.

### `MD_IMPOC` — retail / point-of-sale, v1.0.0

**170 skills · 23 agents**

The first implementation, and the source of the software-delivery lineage:
security (29 skills), PostgreSQL, Node/Express, React, architecture, QA,
performance, planning, UI/UX, design systems, and business analytics.

Its enduring contribution is the **operating model** — the review gate, the
severity scale, the escalation list, and the rule that the agent which writes a
fix never verifies it.

### `MD_eventina` — event-organiser portfolio, v1.0.0

**38 skills · 8 agents**

Built lean and standalone for a portfolio site whose job is to convey the emotion
of different kinds of events. Introduced the creative lineage: art direction,
editorial layout and typography, colour and motion, the media pipeline, and
discovery for a local service business.

Two things worth reusing rather than re-deriving:

- The **event-type → emotion → pace/crop/colour/type/motion** mapping in
  `emotional-brief`
- **`media-consent`**, built on India's DPDP Rules 2025, with consent as
  build-gated data and a seven-step erasure path including CDN purge

### `MD_creative_image_photography` — photography & film, v1.1.0

**45 skills · 10 agents**

Branched from `MD_eventina` for a studio that *shoots* the work rather than
commissioning it. Adds the `photography-craft` pack:

| Skill | Covers |
|---|---|
| `signature-style` | The eight-axis tally, the refusal, the mixed-grid test that can fail |
| `wedding-story-arc` | Five movements; the multi-day function table |
| `film-showcase` | The film ladder, sound-first design, music licensing as a build gate |
| `client-gallery-delivery` | Delivery as a separate system — separate host, `noindex`, never in the sitemap |
| `booking-and-packages` | The four-level pricing disclosure ladder |
| `image-rights-and-credit` | IPTC embedding, watermark policy, licensable-image markup |
| `vendor-network` | Credit and share-back as the referral mechanism |

Plus two agents — `cinematographer` and `client-experience-lead`.

---

## Design principles

**Expertise lives in skills, not personas.**
The harness writes each agent's `identity.md` itself — it cannot be hand-authored.
An agent's capability is therefore its **installed skills** plus the **objective**
it is spawned with. That constraint shapes the whole framework, and is why it is
mostly skills, and why personalisation rewrites objectives.

**Every claim is cited.**
Each skill cites its sources specifically — a standard and its section, not just
an organisation name — and ends with a note naming what is *not* sourced and was
written for this framework. You can tell established practice from opinion at a
glance.

**Principles first, one domain as the worked example.**
Guidance is written so that changing a library adds a row to a table rather than
invalidating the skill. **The principle is portable; the example is not** — which
is exactly what personalisation pass 3 exists to fix. `MD_generic` states plainly
which packs still carry their lineage's vocabulary rather than pretending the
skills are example-free.

**Ask, do not infer.**
The domain, the audience, the client's facts, and the business rules are not
derivable from a repository. Guessing them produces work that is competent and
subtly foreign.

**Discovery is blocking.**
The framework assumes a **greyfield** reality: a codebase or a client that
already exists and is not fully documented. Until the architecture, schema, API,
business rules, and client facts are documented and confirmed, implementation
agents stay read-only. Agents that guess at conventions produce plausible code
that quietly violates the system.

**Client facts are three-state.**
Verified / `[to verify]` / unknown — and **only the first may be published.** A
directory listing, an old brochure, and an inference from a domain name are all
`[to verify]`. A wrong trading name reaches every page title, the structured
data, and the copyright embedded in every published image.

**The agent that writes a fix never verifies it.**
Self-verification is not verification: an agent checking its own work re-derives
the same reasoning and finds the same nothing.

---

## ⚠️ Harness formats are a snapshot, not a contract

Every harness-facing format in these branches — roster entries, spawn requests,
skill locations, message verbs — was verified against **Munder Difflin v0.4.5**
on **2026-09-06** by reading a live install.

**Munder Difflin is under active development.** The harness ships its own
`PROTOCOL.md` and `COMMANDS.md` in the hive root, and **those update with the
app** — where they disagree with anything in these branches, the harness wins.
The right response to drift is to **translate, not force**: these skills are
documents, and their content is independent of how the harness packages them.

Two constraints discovered from a live install shaped everything:

1. **`identity.md` is written by the harness and is read-only.**
2. **Skills are Claude Code's native `SKILL.md`, in a flat namespace** —
   `agents/<id>/.claude/skills/<name>/SKILL.md`. Names must be globally unique
   across every installed skill, which is a real constraint when merging
   branches.

---

## Getting started

Clone the branch you want. For a new project, that is `MD_generic`:

```bash
git clone -b MD_generic https://github.com/rider4585/MD_custome_framework.git
```

Then:

- **If you are a human:** follow `QUICKSTART.md` on that branch.
- **If you are the orchestrator agent:** read `BOOTSTRAP.md` and work through its
  twelve steps. Step 6 is the interview.

**Before anything works:** enable `orchestratorMaySpawn` in
Settings → Autonomy & Budgets. It is off by default, and while it is off spawn
requests wait in `spawn-requests/` rather than failing — which looks like a hang.

---

## Status

| Branch | Status |
|---|---|
| `MD_generic` | v2.0.0 — complete. **Use this for new projects** |
| `MD_IMPOC` | v1.0.0 — complete and installable |
| `MD_eventina` | v1.0.0 — complete; client facts unverified, Phase 0 blocking |
| `MD_creative_image_photography` | v1.1.0 — complete; one client fact verified, Phase 0 blocking |

**Known gap:** `SOURCES.md` on `MD_generic` consolidates the bibliography for the
creative lineage (~60 skills). The software-delivery lineage cites inline per
skill and has not been consolidated. This is stated in the file rather than
papered over.

## Licence

Private. Not for redistribution. Third-party resources keep their own licences —
see `SOURCES.md` on any implementation branch.
