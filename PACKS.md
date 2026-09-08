# Packs

**219 skills across 23 categories. No project installs them all.**

This file is the manifest the orchestrator reads during onboarding to decide
what exists for a given project → `framework-personalisation` pass 1.

The organising idea: every skill is **core**, **stack**, or **vertical**.

| Tier | Meaning | Install |
|---|---|---|
| **Core** | The principle holds for any project in that lane | By lane, for the agents in it |
| **Stack** | Tied to a specific technology | Only if the project uses it |
| **Vertical** | Tied to an industry or a business model | Only if the domain matches |

---

## The onboarding rule

**Lean beats complete.** Every installed skill consumes context on every session.
An agent with sixty skills reads worse than one with twelve at *everything*, and
the failure is silent — nothing errors, the work is just slightly worse.

**Target twelve skills per agent. Twenty is a lot. Thirty is a problem.**

---

## Always

| Skill | Why |
|---|---|
| `project-context` | Every agent, every session. Nothing works without it |
| `project-client-brand` | Any agent that writes something a customer will read |

The `onboarding` pack goes on the **orchestrator only**.

| Pack | Skills | Contains |
|---|---|---|
| `onboarding` | 3 | `project-discovery`, `framework-personalisation`, `agent-roster-design` |

---

## Core packs

Domain-neutral. The principle transfers; the worked examples may not →
`framework-personalisation` pass 3.

| Pack | Skills | Install when | Specificity of examples |
|---|---|---|---|
| `planning` | 8 | Any project with more than one person | Low |
| `architecture` | 10 | Any system with structure worth defending | Medium — leans transactional/business systems |
| `security` | 29 | **Any project handling money, personal data, auth, or user input** | Medium — web application assumptions |
| `qa-testing` | 15 | Anything that ships | Medium |
| `performance` | 6 | Anything with users waiting on it | Low |
| `uiux` | 9 | Anything with an interface | Medium |
| `design-system` | 7 | More than a handful of screens | Low |
| `experience-quality` | 5 | Client-facing surfaces; launch gates | Low |
| `content-story` | 4 | Anything with words a customer reads | Low |
| `creative-direction` | 7 | Anything where how it feels is part of the product | Medium — event/portfolio examples |
| `discovery` | 5 | Anything that needs to be found | Medium — local-business assumptions |
| `media-pipeline` | 6 | Image- or video-heavy projects | Low |
| `project-knowledge` | 15 | Templates. Fill the ones that apply, delete the rest | Templates |

**`security` is the pack most often wrongly skipped.** Twenty-nine skills is too
many for one agent — install the subset that matches the threat surface, and give
the whole pack only to a dedicated security lead → `agent-roster-design`.

---

## Stack packs

Install only if the project uses the technology. If it uses a different one, the
*patterns* usually transfer and the *commands* do not — re-example rather than
following them literally.

| Pack | Skills | Tied to |
|---|---|---|
| `nodejs-backend` | 11 | Node, Express |
| `react-frontend` | 13 | React |
| `postgresql` | 15 | PostgreSQL |
| `web-craft` | 7 | Astro, static site generation, git-based CMS |

---

## Vertical packs

Tied to an industry or business model. **Installing the wrong vertical is worse
than installing none** — it introduces a bias nobody sees.

| Pack | Skills | Domain | Also useful for |
|---|---|---|---|
| `retail` | 8 | Point-of-sale, inventory, multi-shop | Hospitality, rentals, any stock-and-till business |
| `photography-craft` | 7 | Photography and film services | Any creative service sold on a portfolio: design, architecture, events, video |
| `business-analytics` | 14 | Reporting and analysis as a deliverable | Any project where a report is the product |
| `customer-intelligence` | 8 | Customer data, segmentation, feedback | CRM, loyalty, subscription businesses |
| `marketing` | 7 | Campaigns, lifecycle, acquisition | Any project with a growth mandate |

**When a vertical nearly fits**, install it and re-example it rather than writing
a new pack from nothing. The structure of the thinking usually transfers even
when the vocabulary does not — say in the report that you did this →
`framework-personalisation`.

---

## Agents and their packs

Which agents pair with which packs. The agent files ship with a suggested
install line; **treat it as a starting point and cut it to the project** →
`agent-roster-design`.

| Agent group | Agents | Primary packs |
|---|---|---|
| `management` | architect, feature-planner, michael, creative-director | `planning`, `architecture`, `onboarding`, `project-knowledge` |
| `engineering` | backend-engineer, frontend-engineer, frontend-reviewer, postgres-specialist, astro-engineer, media-engineer | `nodejs-backend`, `react-frontend`, `postgresql`, `web-craft`, `media-pipeline` |
| `security` | security-lead, code/api-security-reviewer, threat-modeler, dependency-auditor, security-verifier | `security` |
| `quality` | qa-engineer, e2e-tester, performance-engineer, experience-qa | `qa-testing`, `performance`, `experience-quality` |
| `uiux` | uiux-designer, design-system-guardian | `uiux`, `design-system` |
| `creative` | art-director, brand-strategist, content-writer, cinematographer | `creative-direction`, `content-story`, `media-pipeline`, + `photography-craft` if the domain fits |
| `growth` | discovery-specialist, client-experience-lead | `discovery`, `marketing`, + `photography-craft` for the commercial-journey skills |
| `business-intelligence` | sales-analyst, inventory-analyst, retail-strategist, campaign-strategist, customer-feedback-analyst | `business-analytics`, `customer-intelligence`, + `retail` if the domain fits |

**Several creative and growth agents ship with vertical-pack skills in their
suggested install line** — `signature-style`, `wedding-story-arc`,
`film-showcase`, `booking-and-packages`, `client-gallery-delivery`,
`image-rights-and-credit`, `vendor-network`. Those are from `photography-craft`.
Keep them for a creative-services project, drop or re-example them otherwise.

---

## Specificity, honestly

**This framework does not claim to be example-free.** Its convention is
*principles first, one domain as the worked example* — which makes the principles
portable and the examples not.

Two lineages produced these skills, and their examples show it:

- **Retail / point-of-sale** — `architecture`, `security`, `qa-testing`,
  `postgresql`, `nodejs-backend`, `react-frontend`, `business-analytics`,
  `customer-intelligence`, `marketing`, `uiux`, `design-system`, `planning`,
  `performance`, `retail`
- **Creative portfolio sites** — `creative-direction`, `web-craft`,
  `media-pipeline`, `discovery`, `content-story`, `experience-quality`,
  `photography-craft`

**Pass 3 of `framework-personalisation` exists to fix this per project.** Read
the skill, change the nouns in the examples, leave the pattern and the citations
alone, bump the version. Do not run a blind find-and-replace: it produces
sentences that are grammatical and false, which is the worst output available.

---

## Checking what an agent has

```bash
./bin/install-skills.sh --list                 # everything available, by pack
./bin/install-skills.sh --agents               # agents in the hive
./bin/install-skills.sh --installed <agent-id> # what one agent actually has
```

**Not sourced — written for this framework:** the three-tier model, the
twelve-skill target, the pack tables and their install conditions, the
wrong-vertical-is-worse-than-none rule, and the specificity disclosure above.
