# munder-difflin-agents — `MD_generic`

An agent and skill framework for the
[Munder Difflin](https://github.com/chaitanyagiri/munder-difflin) multi-agent
harness. **Domain-neutral by design**: it ships knowing how to do the work well
in general, and learns your project by asking you.

**219 skills · 33 agents · 23 packs · an onboarding interview that specialises
all of it**

---

## What this is

The other branches in this repository are each built for one client. This one is
built for **the next** client — a superset of the skills and agents, with the
project-specific facts stripped out and replaced by the machinery for asking.

### How it works

```
1. Install          the orchestrator installs the onboarding pack into itself
2. INTERVIEW        it asks YOU: what is this, for whom, what domain, what is
                    explicitly not in scope                    ← the whole point
3. Personalise      it selects packs, fills project-knowledge, rewrites the
                    worked examples into your domain, retunes agent objectives
4. Phase 0          discovery agents document what already exists
5. Work             everything else
```

**Steps 2 and 3 are blocking.** An orchestrator that skips them and infers your
project from the repository will be plausibly wrong, and every agent downstream
inherits the error without ever seeing the assumption that produced it.

A repository tells you what was built. It does not tell you what it is *for*.

### Branch model

| Branch | Domain |
|---|---|
| `main` | Project README only |
| `MD_IMPOC` | Retail / point-of-sale — 170 skills, 23 agents |
| `MD_eventina` | Event-organiser portfolio — 38 skills, 8 agents |
| `MD_creative_image_photography` | Photography & film portfolio — 45 skills, 10 agents |
| **`MD_generic`** | **Domain-neutral superset — this branch** |

This branch merges `MD_IMPOC` and `MD_creative_image_photography`. **Start new
projects here**; the client branches are the record of how a specialised
framework ended up looking, and are useful as worked examples of step 3.

## ⚠️ Formats are a snapshot, not a contract

Every harness-facing format here — roster entries, spawn requests, skill
locations, message verbs — was verified against **Munder Difflin v0.4.5** on
**2026-09-06** by reading a live install.

**Munder Difflin is under active development.** The harness ships its own
`PROTOCOL.md` and `COMMANDS.md` in the hive root, and **those update with the
app** — where they disagree with anything here, the harness wins. The right
response to drift is to **translate, not force**: these skills are documents, and
their content is independent of how the harness packages them.

Two constraints that shaped the repository, both discovered from a live install:

1. **`identity.md` is written by the harness and is read-only.** You cannot
   hand-author an agent's persona. An agent's expertise comes from **the skills
   installed into its `.claude/skills/`** plus **the `objective` given at spawn
   time**. That is why this repo is mostly skills, and why personalisation
   pass 4 rewrites objectives.
2. **Skills are Claude Code's native `SKILL.md`, in a flat namespace** —
   `agents/<id>/.claude/skills/<name>/SKILL.md`. Names must be globally unique.

## Layout

```
├── BOOTSTRAP.md                      ← START HERE if you are the orchestrator
├── GOD-PLAYBOOK.md                   ← the orchestrator reads this each session
├── PACKS.md                          ← which skills exist for which project
├── QUICKSTART.md                     ← human setup guide
├── SOURCES.md                        ← consolidated bibliography, with licences
├── agents/<category>/*.md            ← 33 agents: roster entry, skills, objective
├── skills/<category>/<name>/SKILL.md ← 219 skills across 23 packs
├── templates/                        ← agent and skill templates
└── bin/install-skills.sh             ← installs skills into an agent
```

## The onboarding pack

Three skills, installed on the orchestrator only. They are the difference
between this branch and a pile of good documents.

| Skill | Does |
|---|---|
| `project-discovery` | The intake interview — six sections, one batched message, `unknown` recorded rather than guessed |
| `framework-personalisation` | Four passes: select packs → fill project-knowledge → **re-example** → retune objectives |
| `agent-roster-design` | Which of the 33 agents this project actually needs, and when a role should be a skill instead |

Their output lands in `project-context` — the root file every other agent reads
first, every session.

## The packs

| Tier | Packs |
|---|---|
| **Always** | `project-context`, `project-client-brand`, and `onboarding` on the orchestrator |
| **Core** | `planning`, `architecture`, `security`, `qa-testing`, `performance`, `uiux`, `design-system`, `experience-quality`, `content-story`, `creative-direction`, `discovery`, `media-pipeline`, `project-knowledge` |
| **Stack** | `nodejs-backend`, `react-frontend`, `postgresql`, `web-craft` |
| **Vertical** | `retail`, `photography-craft`, `business-analytics`, `customer-intelligence`, `marketing` |

**See [`PACKS.md`](PACKS.md)** for what each contains, when to install it, and an
honest note on how domain-flavoured its worked examples are.

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

Skills are written **principles first, one domain as the worked example**. The
principle is portable; the example is not — which is exactly what
personalisation pass 3 exists to fix.

## The rules that recur

- **Ask, do not infer.** The domain, the audience, the client's facts, the
  business rules — none of these are derivable from a repository
- **Lean beats complete.** Twelve skills per agent. An agent with sixty reads
  worse at everything, and nothing errors to tell you
- **The agent that writes a fix never verifies it**
- **Phase 0 is blocking.** No code written against guessed conventions
- **Never publish an unverified fact about a client.** Verified / `[to verify]` /
  unknown, and only the first ships
- **Batch questions to the human.** Trickling them out is how discovery fails
- **`unknown` is a valid answer; a guess is not**
- **Content is the critical path on client work**, not development
- **Personalise the hive copies, never the repository** — or every project ends
  up looking like the last one

## Getting started

**If you are the orchestrator agent:** read **[BOOTSTRAP.md](BOOTSTRAP.md)** and
work through its twelve steps. Step 6 is the interview.

**If you are a human:** see **[QUICKSTART.md](QUICKSTART.md)**. The short
version:

```bash
./bin/install-skills.sh --agents          # what's in your hive
./bin/install-skills.sh --list            # what's available
./bin/install-skills.sh <orchestrator-id> project-discovery framework-personalisation agent-roster-design project-context
```

Then restart the orchestrator and point it at [`BOOTSTRAP.md`](BOOTSTRAP.md).

**Before anything works:** enable `orchestratorMaySpawn` in
Settings → Autonomy & Budgets. It is off by default, and without it the
orchestrator cannot spawn.

## Licence

Private. Not for redistribution. Third-party resources keep their own licences —
see [`SOURCES.md`](SOURCES.md).
