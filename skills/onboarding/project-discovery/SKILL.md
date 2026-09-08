---
name: project-discovery
version: 1.0.0
description: |
  The intake interview the orchestrator runs with the human immediately after
  the framework is installed, and before any other work. Turns a generic
  framework into a project-specific one. Use when the framework has just been
  installed, when project-context is still a template, when the project pivots,
  or when the user says "here is what we are building".
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
---

## Project Discovery

**This framework ships generic on purpose, and it is not usable until this
interview has been run.** The skills describe how to do the work well in general;
they do not know what your project is. This skill is the bridge.

The rule that makes it work: **ask, do not infer.** An orchestrator that guesses
the domain by reading the repository will be plausibly wrong, and every agent
downstream will inherit the error without ever seeing the assumption that
produced it.

> **Before running anything:** confirm the framework is actually installed —
> `./bin/install-skills.sh --agents` and `--installed <agent>`. There is no point
> interviewing before there is anywhere to put the answers.

### Method

1. **Check whether this has already been done.** Read
   `project-context`. If it is filled, this is a *review*, not an intake — ask
   only what has changed.
2. **Run the interview below** with the human, in one conversation.
3. **Batch the questions.** Send them as one message, grouped, not one at a
   time. A twenty-message interrogation gets abandoned.
4. **Write the answers into `project-context`** immediately, including the
   `unknown`s.
5. **Then personalise** → `framework-personalisation`, `agent-roster-design`.
6. **Report back** what you filled, what is still unknown, and what that blocks.

### The interview

Ask all of it. Mark anything unanswered as `unknown` rather than dropping it —
an unanswered question is information about the project's state.

**A. What and why** — fills `project-context`

1. In one sentence, what are we building?
2. What changes if this succeeds? *(the outcome, not the artefact)*
3. What does "done" look like? How will we know?
4. What is explicitly **not** in scope? *(push here — this is the field most
   often left empty and the one that saves the most time later)*
5. Is there a deadline, and what is driving it?

**B. Who** — fills `project-context`, `project-client-brand`

6. Who is this for? Primary and secondary audiences.
7. What device and connection will they realistically be on?
8. Who is the client or business? Who decides, who answers questions, and how
   quickly?
9. Is there an existing brand, voice, or design system to obey?

**C. Domain** — decides which packs are installed → `PACKS.md`

10. What industry or vertical is this? *(be specific: "e-commerce" and
    "point-of-sale for a single physical shop" install different skills)*
11. Any regulatory or compliance environment? *(payments, health, children's
    data, accessibility mandate, data residency)*
12. Does this project handle money, personal data, or user-generated content?

**D. Technical** — fills `project-context`, `project-architecture`

13. Greenfield, rewrite, or extension of something existing?
14. What is the stack, and how much of it is already decided?
15. Where does it deploy, and who owns that account?
16. What already exists that we must not break?

**E. The work itself** — fills `project-service-catalogue`,
   `project-content-inventory`

17. What does the client actually sell or do? Which parts do they want more of?
18. What content, data, or assets already exist, and where?
19. What is behind a login that we cannot reach without a human?

**F. Constraints and history**

20. What has been tried before and did not work?
21. What are you worried about?
22. Is there anything we are not allowed to publish, change, or touch?

**Question 20 and 21 are the highest-yield questions in the set** and the ones
most often skipped because they feel unprofessional. They surface the constraint
that would otherwise be discovered in week six.

### Reading the answers

| Signal in the answer | What it means for the build |
|---|---|
| Cannot state the outcome, only the artefact | The goal is unowned. Escalate before building — you will be asked to change direction later |
| Non-goals list is empty | Scope will grow. Propose three non-goals yourself and get them rejected or accepted |
| "Everyone" is the audience | No audience. Push for the one who pays |
| Deadline with no driver behind it | Usually soft. Worth knowing which deadlines are real |
| Existing system nobody understands | Add a discovery phase → `project-architecture`, `project-database` |
| Content "already exists" | Assume unassessed → `project-content-inventory` |
| No answer on who owns the deployment account | Fix this at the start; it is painful at handover |

### What to do with silence

Clients and project owners routinely cannot answer some of these. That is a
finding, not a failure.

- **Record `unknown`** and what it blocks, in the open-questions table.
- **Proceed on everything the unknown does not block.** Do not stall the whole
  project on one open question.
- **State the assumption in writing** where you must proceed anyway, and make it
  easy to reverse.
- **Never fill a gap by inference and then forget you did.** If you must assume,
  the assumption is recorded as an assumption.

### Anti-patterns

- **Reading the repo and skipping the interview.** The repository says what was
  built, not what it is for.
- **Asking one question per message.** The interview is one conversation.
- **Asking for the stack before asking for the goal.** Technical answers arrive
  first because they are easy, and then anchor everything that follows.
- **Treating the first answer to "what is not in scope" as final.** It is
  usually "nothing"; ask again with examples.
- **Running discovery, then not personalising.** The interview is worthless if
  the answers stay in a chat log → `framework-personalisation`.

### Caveats

- **This interview is sized for a client project.** For internal or personal
  work, sections B and C compress to a few lines; do not perform the whole
  ceremony on a two-day tool build.
- **Some answers will be wrong**, sincerely. Clients misdescribe their own
  businesses routinely. Treat answers as `[to verify]` where they are checkable
  → `project-client-brand`.
- **Re-run section A after any pivot.** A changed goal invalidates decisions
  that were correct when they were made.

### Checklist

- [ ] `project-context` checked first — intake vs. review established
- [ ] All six sections asked, in one batched message
- [ ] Answers written into `project-context` the same session
- [ ] `unknown` recorded rather than guessed, with what it blocks
- [ ] Non-goals pushed on until the list is non-empty
- [ ] Domain specific enough to select packs
- [ ] Compliance environment established
- [ ] Existing-system and login-walled gaps named with a human owner
- [ ] Assumptions recorded as assumptions
- [ ] Personalisation run immediately afterwards
- [ ] A report sent back naming what is filled, unknown, and blocked

## References

- **`project-context`, `project-client-brand`, `project-service-catalogue`,
  `project-content-inventory`, `project-architecture`** (this framework) — the
  files this interview fills
- **`framework-personalisation`, `agent-roster-design`** (this framework) — what
  runs next
- **`PACKS.md`** (this repository) — how the domain answer selects skill packs
- **`BOOTSTRAP.md`** (this repository) — where this interview sits in the
  install sequence

**Not sourced — written for this framework:** the entire question set, the
ask-do-not-infer rule, the answer-signal table, the silence procedure, and the
anti-patterns.
