# Quickstart

Setting up the `MD_generic` framework in a Munder Difflin hive. About 30 minutes,
plus the intake interview.

**This branch ships domain-neutral.** Setup is: install → the orchestrator
interviews you → it personalises everything → work begins. Steps 5 and 6 below
are the ones that matter.

---

## 1. Enable spawning

**Settings → Autonomy & Budgets → enable `orchestratorMaySpawn`.**

Off by default. While it is off, spawn requests wait in `spawn-requests/` rather
than failing, which looks like a hang.

## 2. Create the orchestrator

Agent creation is a UI action — it cannot be scripted from here.

Create **one** orchestrator from either `agents/management/michael.md` (system
and product work) or `agents/management/creative-director.md` (client-facing
creative work). **Not both** — two agents writing `board.md` is how a hive loses
track of its own state.

Use the `id`, `name`, `character`, `accent`, `description`, `command`,
`provider`, and `model` from its roster entry block. Set:

- **`cwd`** → the **project** repository, not this framework repository
- **`project`** → your project's name (the files ship with `<PROJECT>`)

**Start it once** so the harness creates its directory. `install-skills.sh`
refuses to write into an agent that has never run.

**Do not create the other 32 agents yet.** Which ones this project needs is a
decision the orchestrator makes in step 6 → `agent-roster-design`.

## 3. Install the onboarding pack

```bash
cd "/path/to/Munder Difflin Framework"
git checkout MD_generic

./bin/install-skills.sh --agents                    # confirm it exists
./bin/install-skills.sh <orchestrator-id> \
  project-discovery framework-personalisation agent-roster-design \
  project-context project-client-brand \
  requirements-analysis task-decomposition implementation-plan
```

Verify:

```bash
./bin/install-skills.sh --installed <orchestrator-id>
```

## 4. Restart the orchestrator

**Claude Code reads `.claude/skills/` at session start.** The agent cannot see
newly installed skills, and cannot restart itself. This is the step people miss.

## 5. Point it at the bootstrap

In the orchestrator's session:

> Read `BOOTSTRAP.md` in the framework repo at `<path>` and work through it.

It will verify the harness formats, locate everything, install what it needs, and
then come back to you with the interview.

## 6. Answer the interview ⭐

**This is the step that makes the framework yours.** The orchestrator will send
one batched message covering six sections:

| | Section |
|---|---|
| **A** | What and why — the one-liner, the outcome, what "done" looks like, **what is explicitly not in scope**, the deadline |
| **B** | Who — audiences, their real devices and connection, the client, who decides and how fast |
| **C** | Domain — industry, compliance environment, whether it handles money or personal data |
| **D** | Technical — greenfield / rewrite / extension, stack, hosting, what must not break |
| **E** | The work — what is actually sold, what content or data exists, what is behind a login |
| **F** | Constraints — what was tried before and failed, what worries you, what is off-limits |

**Answer section A properly, especially the non-goals.** It is the field most
often left empty and the one that saves the most time later — scope grows through
plausible additions, and a written non-goal turns "could we also…" back into a
decision that was already made.

**"I don't know" is a useful answer.** It gets recorded as `unknown` with what it
blocks, rather than guessed.

## 7. Let it personalise

The orchestrator then runs four passes → `framework-personalisation`:

1. **Selects packs** from your domain answer → `PACKS.md`
2. **Fills the `project-*` knowledge files**, and deletes the ones that do not
   apply to you
3. **Re-examples** the installed skills into your domain — these skills are
   written *principles first, one domain as the worked example*, and the examples
   come from a retail system and a photography studio
4. **Retunes the agent objectives** with your context and your blocking rules

It reports back what it filled, what is still unknown, and **what it left
deliberately generic**. Read that last part — it tells you where the framework
still does not know your project.

## 8. Phase 0 before any building

**Blocking by design.** Discovery agents document what already exists —
architecture, schema, API, business rules, client facts — with every claim marked
`[verified]`, `[inferred]`, or `[assumed]`.

Expect **one batched list of questions** that cannot be answered from the code:
business rules, pricing logic, permission semantics, client facts. Answer them
together; trickling them out is how this phase fails.

Phase 0 exits when nothing load-bearing is still `[assumed]` — money, access,
data integrity, legal claims, or published facts about the client.

## Troubleshooting

| Symptom | Cause |
|---|---|
| `could not determine harnessHome` | Set `MUNDER_HARNESS_HOME` to the hive root |
| `agent "x" not found in the hive` | Use the real suffixed id from `--agents` |
| Skills installed but the agent cannot see them | It has not been restarted (step 4) |
| Spawn request sits in `spawn-requests/` | `orchestratorMaySpawn` is off (step 1) |
| The orchestrator started work without interviewing you | It skipped `BOOTSTRAP.md` step 6. Send it back — an inferred domain is the failure this framework exists to prevent |
| Agents produce competent but subtly foreign work | Personalisation was shallow. Re-run passes 3 and 4 |
| Agents keep asking you the same question | It belongs in `project-context` and was never written down |
| Every project looks like the last one | Someone personalised the repository instead of the hive copies |
| An agent has 60 skills and reads badly | Cut its install line. Twelve is the target → `PACKS.md` |

## Where to read next

| File | For |
|---|---|
| [`BOOTSTRAP.md`](BOOTSTRAP.md) | The orchestrator's twelve-step install and onboarding sequence |
| [`GOD-PLAYBOOK.md`](GOD-PLAYBOOK.md) | The orchestrator's standing operating procedure |
| [`PACKS.md`](PACKS.md) | Which skills exist for which kind of project |
| [`templates/`](templates/) | Formats for adding a skill or an agent |
| [`SOURCES.md`](SOURCES.md) | Bibliography and licences |
