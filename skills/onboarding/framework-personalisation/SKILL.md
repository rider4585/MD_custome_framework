---
name: framework-personalisation
version: 1.0.0
description: |
  Turn the generic framework into this project's framework — select skill packs,
  fill the project-knowledge files, rewrite worked examples into the project's
  domain, and retune agent objectives. Use immediately after project-discovery,
  when a project pivots, or when agents keep producing work that is technically
  right and contextually wrong.
allowed-tools:
  - Read
  - Write
  - Edit
  - Grep
  - Glob
  - Bash
---

## Framework Personalisation

The framework ships generic, which means it ships **slightly wrong for every
project**. Personalisation is the step that makes it right for one.

This is not a formality. An agent working from a generic skill produces work
that is competent and subtly foreign: correct patterns illustrated with a
domain's vocabulary that nobody on the project uses, checklists that include
irrelevant items and omit the one that matters here.

> **Before running anything:** `project-context` must be filled. If it still
> shows template placeholders, run `project-discovery` first — personalising
> against an unfilled context produces a confidently wrong specialisation, which
> is worse than a generic one.

### Method

Four passes, in order. Each is cheap; skipping them is what is expensive.

1. **Select packs** — which skills exist for this project at all
2. **Fill project-knowledge** — the facts every agent reads
3. **Re-example** — rewrite worked examples into this domain
4. **Retune objectives** — the agent briefs

Then **report** what changed, and what you deliberately left generic.

### Pass 1 — Select packs

Read `PACKS.md`. Install the **core** for every agent that needs it, then add
only the packs the domain answer justifies.

**The rule is lean, not complete.** Every installed skill consumes context on
every session; an agent with sixty skills reads worse than one with twelve, and
the failure is invisible — it does not error, it just performs slightly worse at
everything. Twelve is a good target, twenty is a lot, thirty is a problem.

| Signal from `project-context` | Pack |
|---|---|
| Handles money or payments | `security`, and the payment-specific rules within it |
| Personal data, or any regulated data | `security`, `media-consent` |
| Has a database | `postgresql` or the relevant data pack, `project-database` |
| Client-facing marketing site | `web-craft`, `discovery`, `content-story`, `creative-direction` |
| Image- or video-led | `media-pipeline`, `experience-quality` |
| Application UI | `react-frontend`, `design-system`, `uiux` |
| Sells something with a catalogue | `project-service-catalogue`, and the relevant vertical pack |
| Reporting or analytics is a deliverable | `business-analytics`, `customer-intelligence` |

**When a vertical pack nearly fits but is for the wrong industry** — the retail
pack on a hospitality project, say — install it and re-example it (pass 3)
rather than writing a new pack from nothing. The structure of the thinking
usually transfers even when the vocabulary does not. Say in your report that you
did this.

**When no pack fits**, do not force one. Note the gap; a missing pack is a
visible absence, whereas a wrong pack is an invisible bias.

### Pass 2 — Fill project-knowledge

Every `project-*` skill ships as a template with a 🟡 banner. Fill them in
dependency order, because each depends on the one before:

```
project-context           ← first, always. Everything hangs off it
  └── project-client-brand      the facts, and what is unverified
        └── project-service-catalogue   what is sold
              └── project-content-inventory   what exists to build with
                    └── project-site-architecture / project-architecture
                          └── project-roadmap
```

Rules:

- **Remove the 🟡 banner when a file is genuinely filled**, and not before. The
  banner is how every other agent knows whether to trust the file.
- **A file that does not apply gets deleted, not left blank.** An empty
  `project-database` on a static site is noise that every agent pays for.
- **Keep the ⛔ Phase 0 gate** in `project-client-brand` whatever the domain. The
  unverified-facts rule is not portfolio-specific; it protects every client.
- **`unknown` is a valid fill.** A field marked unknown routes itself to the open
  questions; a field guessed does not.

### Pass 3 — Re-example

**This is the pass everyone skips and the one that produces most of the value.**

Skills in this framework follow a convention: **principles first, one domain as
the worked example.** The principle is portable; the example is not. A skill
written with a retail example is not a retail skill — but an agent reading it
all day will start to think in retail.

For each installed skill, check the worked examples and rewrite the ones that
carry a foreign domain:

```bash
# Find domain vocabulary that does not belong to this project.
# Replace the terms with whatever the previous domain used.
grep -rniw -e 'retail' -e 'wedding' -e 'cashier' -e 'photographer' \
  "$HARNESS_HOME/hive/agents/<agent-id>/.claude/skills/" \
  | cut -d: -f1 | sort | uniq -c | sort -rn
```

**What to change, and what to leave:**

| Change | Leave alone |
|---|---|
| Entity names in examples — `orders` → your nouns | The pattern being demonstrated |
| Sample data, table names, routes, slugs | Cited standards and their section numbers |
| Domain vocabulary in prose | The `Not sourced` note — it stays true |
| Checklist items that name a foreign domain | Checklist items that are domain-neutral |
| The `description` trigger phrases, if the triggers are domain words | `name`, and the version unless you changed substance |

**Bump the version and note the change** when you alter a skill's substance, so
a later reader can tell a personalised skill from a shipped one. Re-exampling is
substance.

**Do not re-example a skill you have not read.** Blind find-and-replace across a
skill produces sentences that are grammatically fine and technically false — the
worst possible output, because it reads as authoritative.

### Pass 4 — Retune objectives

An agent's expertise comes from two places: its installed skills, and the
`objective` it is spawned with. The objectives shipped in `agents/*.md` are
written against a generic project and **must be rewritten before use.**

For each agent you are instantiating:

1. **Replace the domain sentence.** The first line establishes what the agent is
   working on. Take it from `project-context`'s one-liner.
2. **Set `cwd` and `project`** to the real repository and project name.
3. **Import the project's blocking gates.** If this project has a hard rule —
   unverified facts, consent, licensing, a compliance boundary — it goes in the
   objective in capitals, not only in a skill. Agents obey objectives more
   reliably than they obey documents.
4. **Cut the paragraphs that do not apply.** An objective carrying instructions
   for work this project does not do teaches the agent to ignore its objective.
5. **Keep the boundary line.** Every objective ends by naming what the agent must
   not do. That line is what stops agents doing each other's work.

### Report back

Personalisation is invisible unless you report it. Send the human:

- Which packs were installed, per agent, and the skill count each
- Which `project-*` files are filled, which are `unknown`, and what that blocks
- Which skills were re-exampled, and which were deliberately left generic
- Which agents were retuned, and the gates written into their objectives
- **What you could not personalise because you lack an answer** — this is the
  most useful part of the report

### Caveats

- **Personalisation is not a one-time step.** A pivot invalidates it. Re-run
  passes 2–4 after any change to the goal or domain.
- **Re-exampling can introduce errors** that the original skill did not have.
  The original was checked; your rewrite was not. Change examples, not the
  technical claims they illustrate.
- **Resist deleting skills that feel irrelevant** during pass 1 if the project is
  young. Under-installing is cheap to fix; discovering in week eight that nobody
  had the security pack is not.
- **Do not personalise the templates in this repository.** Personalise the
  *installed copies* in the hive. The repository stays generic so the next
  project can start from it — that is the entire point of this branch.

### Checklist

- [ ] `project-context` filled before starting
- [ ] Packs selected from the domain, not from enthusiasm
- [ ] Per-agent skill count kept lean; outliers justified
- [ ] `project-*` files filled in dependency order
- [ ] 🟡 banners removed only from genuinely filled files
- [ ] Inapplicable `project-*` files deleted, not left blank
- [ ] Phase 0 gate retained in `project-client-brand`
- [ ] Installed skills re-exampled into this domain, having been read first
- [ ] Versions bumped where substance changed
- [ ] Agent objectives rewritten, with `cwd`, `project`, and the blocking gates
- [ ] Repository templates left generic; only hive copies personalised
- [ ] Report sent, including what could not be personalised and why

## References

- **`project-discovery`** (this framework) — the interview that must run first
- **`agent-roster-design`** (this framework) — which agents to instantiate
- **`project-context`** and the other `project-*` skills — what pass 2 fills
- **`PACKS.md`** (this repository) — the pack manifest pass 1 reads
- **`templates/skill-template.md`, `templates/agent-template.md`** (this
  repository) — the formats to preserve while editing
- **Munder Difflin `PROTOCOL.md`** in the hive root — the authority on objective
  and roster fields. **The harness wins** over anything written here

**Not sourced — written for this framework:** the four-pass model, the lean-not-
complete rule and its context-cost argument, the dependency order for filling
project-knowledge, the re-example convention and its change/leave table, the
objective retuning steps, and the report contents.
