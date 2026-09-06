# MD_custome_framework

A production-grade agent and skill framework for the
[Munder Difflin](https://github.com/chaitanyagiri/munder-difflin) multi-agent
harness.

Munder Difflin wraps CLI coding agents (Claude Code, Codex, and others) and runs
them as a coordinating team. It ships with generic preconfigured agents. This
project replaces them with a **specialised, opinionated team** — agents that know
a specific stack and domain, each carrying a curated set of skills.

---

## Branches

| Branch | Contents |
|--------|----------|
| **`main`** | This README only — project information |
| **[`MD_IMPOC`](../../tree/MD_IMPOC)** | The full framework, specialised for a retail inventory + point-of-sale system |

The framework is designed to be **branched per domain**. `MD_IMPOC` is the first
implementation; a project in a different vertical gets its own branch, keeping
the domain-independent skills and swapping the domain-specific ones.

---

## What a branch contains

| | |
|---|---|
| **170 skills** | Claude Code `SKILL.md` files across security, database, backend, frontend, architecture, planning, QA, performance, UI/UX, design systems, and business analysis |
| **23 agents** | Roster entries, spawn objectives, and curated skill manifests |
| **Orchestrator playbook** | Routing authority for the GOD agent, written against the live hive protocol |
| **Install tooling** | A script that installs skills into an agent's `.claude/skills/` |

---

## Design principles

**Expertise lives in skills, not personas.**
The harness writes each agent's `identity.md` itself — it cannot be hand-authored.
An agent's capability is therefore its **installed skills** plus the **objective**
it is spawned with. That constraint shapes the whole framework, and is why it is
mostly skills.

**Every claim is cited.**
Each skill cites its sources specifically — a standard and its section, not just
an organisation name — and ends with a note naming what is *not* sourced and was
written for this framework. You can tell established practice from opinion at a
glance.

**Principles are stack-agnostic; examples are concrete.**
Guidance is written so that changing a library adds a row to a table rather than
invalidating the skill. The current stack appears as the worked example.

**Discovery is blocking.**
The framework assumes a **greyfield** reality: a codebase that already exists and
is not fully documented. Until its architecture, schema, API, and business rules
have been documented and confirmed, engineering agents stay read-only. Agents
that guess at conventions produce plausible code that quietly violates the
system.

---

## Getting started

Clone the implementation branch:

```bash
git clone -b MD_IMPOC https://github.com/rider4585/MD_custome_framework.git
```

Setup instructions are in `QUICKSTART.md` on that branch.

---

## Status

`MD_IMPOC` — v1.0.0. Complete and ready to install.

## Licence

Private. Not for redistribution.
