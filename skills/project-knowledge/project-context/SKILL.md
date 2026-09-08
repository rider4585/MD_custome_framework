---
name: project-context
version: 1.0.0
description: |
  The one-page answer to "what is this project, for whom, and what does done
  look like" — goal, users, domain, stack, constraints, and explicit non-goals.
  Every agent loads this first, every session. Use at the start of any session,
  when scope is being argued about, or when deciding whether a piece of work
  belongs in this project at all.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
---

## Project: Context

This is the **root file of the whole framework**. Every other project-knowledge
skill hangs off it, every agent objective is written against it, and every
"should we build this?" argument is settled by it.

It is deliberately short. A context file nobody rereads is a context file nobody
follows, so keep it to one screen and push detail into the other
`project-*` skills.

> ## 🟡 This file is a TEMPLATE until onboarding fills it
>
> The framework ships generic. **The orchestrator fills this file first**, from
> the intake interview → `project-discovery`, before any other work begins.
>
> **An unfilled `project-context` is a blocking state.** An agent that finds the
> placeholders still in place must stop and escalate, not guess a domain from
> the repository contents. Inferring the project from the code is exactly the
> failure this file exists to prevent.

### The file

Fill every field. `unknown` is a valid, useful answer; a blank is not.

```markdown
# Project Context — <project name>

**Last updated:** <date> by <who>

## One line
<What this is, in one sentence a stranger would understand.>

## The goal
<What changes in the world if this succeeds. Not "build a website" — what the
website is supposed to cause.>

## Done looks like
<The condition under which this project is finished. Testable if possible.>

## Who it is for
| Audience | What they arrive wanting | What they leave with |
|---|---|---|
| <primary> | | |
| <secondary> | | |

## The client
<Who is paying, who decides, who answers questions, and how fast. See
project-client-brand for the facts.>

## Domain
<The industry, the vertical, the regulatory environment. This decides which
skill packs are installed — see PACKS.md.>

## Stack and constraints
| | |
|---|---|
| Stack | |
| Hosting / deployment | |
| Existing system? | greenfield / rewrite / extension of <what> |
| Hard constraints | budget, deadline, compliance, accessibility target |
| Audience's real devices and network | |

## Non-goals
<What this project is explicitly NOT doing. The most valuable section in the
file, and the one most often left empty.>

## Open questions blocking work
| Question | Blocks | Asked on | Answer |
|---|---|---|---|
```

### Why non-goals earn their place

Scope grows through plausible additions, each defensible on its own. A written
non-goal converts "could we also…" from a design discussion into a decision that
was already made, and moves the burden onto whoever wants to reopen it.

Write non-goals as **things a reasonable person would otherwise assume are
included**. "Not building a mobile app" is useful. "Not building a spacecraft"
is not.

### Keeping it true

- **One owner.** An unowned context file is trusted long after it stops being
  accurate, which is worse than not having one.
- **Update it when a decision changes it**, not on a schedule.
- **Record the date and the person** on every update.
- **When the goal changes, say so loudly.** A quietly-edited goal invalidates
  work that was correct when it was done, and people should know that happened.

### How agents use it

| Agent question | Field that answers it |
|---|---|
| Should we build this? | Goal, Done, Non-goals |
| Who am I writing/designing for? | Who it is for |
| Which conventions apply? | Stack and constraints, Domain |
| Can I publish this claim? | The client → `project-client-brand` |
| Is this project finished? | Done looks like |

**If the answer is not in this file and not in another `project-*` skill,
escalate — do not decide it privately.** A domain assumption made silently by
one agent propagates into everything downstream and is expensive to unwind.

### Caveats

- **This file records decisions; it does not make them.** Everything in it comes
  from the client or the project owner.
- **Short is the point.** If it grows past a screen, the excess belongs in
  `project-architecture`, `project-service-catalogue`, or `project-roadmap`.
- **Do not infer the domain from the repository.** A repository tells you what
  was built, not what it is for, and the two diverge constantly.
- **`unknown` is honest and useful.** A field confidently filled with a guess is
  the failure mode; a field marked unknown routes itself to the open questions
  table.

### Checklist

- [ ] Every field filled, `unknown` where genuinely unknown
- [ ] The goal states an outcome, not an activity
- [ ] "Done looks like" is testable
- [ ] Non-goals non-empty and non-trivial
- [ ] Domain named, and the pack selection made from it → `PACKS.md`
- [ ] An owner named, with the last-updated date
- [ ] Open questions carry what they block
- [ ] Every agent installs this skill

## References

- **`project-discovery`** (this framework) — the interview that produces this
  file
- **`framework-personalisation`, `agent-roster-design`** (this framework) — what
  is done with it once it exists
- **`project-client-brand`, `project-service-catalogue`, `project-roadmap`,
  `project-architecture`** (this framework) — where the detail lives instead

**Not sourced — written for this framework:** the field set, the unfilled-is-
blocking rule, the non-goals argument, the agent-question mapping, and the
do-not-infer-the-domain rule.
