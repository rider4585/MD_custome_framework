---
name: agent-roster-design
version: 1.0.0
description: |
  Decide which agents this project actually needs, in what order to create them,
  and when a role should be a skill instead of an agent. Use after
  project-discovery, when standing up a new hive, when work is stalling in
  handoffs, or when someone proposes adding another agent.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
---

## Agent Roster Design

This framework ships **33 agent definitions**. Almost no project should
instantiate all of them.

An agent is not free. Each one costs tokens on every session, adds handoff
latency, and creates another place where context can be lost between one
agent's output and another's input. **The cost of an extra agent is paid
continuously; the benefit is paid only when its lane is genuinely busy.**

> **Before running anything:** `project-context` must be filled →
> `project-discovery`. Roster design without a stated goal produces a roster
> that mirrors the framework's categories rather than the project's work.

### Method

1. **List the lanes this project actually has**, from `project-context` — not
   from the framework's directory structure.
2. **Start with three or four agents.** Add the rest as work reaches them.
3. **Apply the tests below** before adding any agent.
4. **Assign models by role**, not uniformly.
5. **Write the roster entries**, then retune each objective →
   `framework-personalisation`.

### The three tests for adding an agent

An agent earns its place only if all three are true:

| Test | Question | If it fails |
|---|---|---|
| **Volume** | Is there enough work in this lane to keep it busy across the project? | Make it a skill installed on an existing agent |
| **Separation** | Does it need to *not* be the agent that did the upstream work? | Merge it upstream |
| **Distinct context** | Does it need a different set of skills, not just a different task? | Merge it |

**The separation test is the one that justifies the most agents**, and it is
worth stating plainly: **the agent that writes a fix must not be the agent that
verifies it.** Self-verification is not verification — an agent checking its own
work re-derives the same reasoning and finds the same nothing. Reviewers,
verifiers, and QA roles pass the separation test even when they fail the volume
test.

**A role that fails all three is a skill, not an agent.** This is the most
common roster mistake: creating a "documentation agent" or an "accessibility
agent" for work that belongs to whoever is already writing the thing.

### Starting rosters

Start here, then grow. These are opinions, argued from the tests above.

| Project shape | Start with | Add when |
|---|---|---|
| **Client marketing / portfolio site** | orchestrator, a design lead, a content writer, a build engineer | QA before launch; discovery once there is something to find; a media engineer once the asset volume is real |
| **Application feature work** | orchestrator, an architect or planner, a build engineer, a reviewer | QA once there are flows to test; security once it handles money or personal data; a data specialist once the schema is non-trivial |
| **Existing system, poorly documented** | orchestrator, a discovery/architecture agent, one build engineer | Everything else after discovery reports. Do not staff a system nobody has mapped |
| **Security or compliance-driven** | orchestrator, a security lead, a build engineer, **a separate verifier** | Threat modelling before design; auditing on a schedule |
| **Analytics or reporting deliverable** | orchestrator, an analyst, a build engineer | A second analyst only if the domains genuinely differ |

**The orchestrator is always first and is always one.** Two agents writing the
board is how a hive loses track of its own state.

### Model assignment

Uniform model assignment wastes money at the bottom and quality at the top.

| Role type | Model | Why |
|---|---|---|
| Orchestrator, architect, creative lead | The strongest available | These decide; a bad decision propagates through everything downstream |
| Reviewers, security, verification | Strong | Missing a real problem is the expensive failure mode |
| Build, implementation, content | Mid-tier | High volume, well-specified work with a spec to check against |
| Mechanical, high-volume, low-judgement | Cheapest that passes | Ingest, transcoding, formatting |

Match `command`, `provider`, and `model` in the roster entry — a mismatch
between them is a common and confusing setup failure →
`templates/agent-template.md`.

### Order of creation

Create agents in the order work reaches them, not all at once:

1. **Orchestrator**, and let it run discovery.
2. **The discovery or planning role** for the project's shape — you are usually
   still learning what this project is.
3. **One implementer.** One, not three. Parallel implementers before the
   architecture is settled produce work that has to be merged and reconciled.
4. **A reviewer or verifier**, as soon as there is output to check.
5. **Everything else**, when its lane has queued work.

**Each agent must be started once** before skills can be installed into it — the
harness creates the agent's directory on first run, and the installer refuses to
write into a directory that does not exist → `BOOTSTRAP.md`.

### When to remove an agent

Rarely done, and worth doing:

- **Its lane finished.** A migration specialist after the migration.
- **It has not been given work in weeks.** It is costing context for nothing.
- **Two agents keep handing the same work back and forth.** That is one lane
  with a boundary drawn in the wrong place. Merge them and redraw.

Archive rather than delete, so the objective and skill list survive for the next
project.

### Anti-patterns

- **Mirroring the framework's directory structure.** `agents/` has eight
  categories; that is a filing decision, not a staffing plan.
- **One agent per skill.** Skills compose onto agents; agents are lanes of work.
- **A "manager" that does not decide anything.** If it only relays, delete it and
  let the orchestrator route directly.
- **Parallel implementers before the architecture is settled.**
- **An agent whose objective is "help with anything".** An unbounded objective
  produces unbounded, unfocused work.
- **Adding an agent to fix a context problem.** If an agent keeps getting things
  wrong, check its installed skills and its objective first — a new agent
  inherits the same gap.

### Caveats

- **These starting rosters are opinions**, tuned for small teams on client work.
  A large programme with real parallelism will justify more agents earlier.
- **Model tiers change.** The assignment table is about relative capability, not
  specific model names — re-read the current model list before committing.
- **Spawning ephemeral workers is a different decision** from adding a roster
  agent, and it needs `orchestratorMaySpawn` enabled → `templates/agent-template.md`.
- **Agent count is not a measure of progress**, though it reliably feels like
  one to everybody involved.

### Checklist

- [ ] `project-context` filled before designing the roster
- [ ] Lanes derived from the project's work, not the framework's directories
- [ ] Started with three or four agents, not the full set
- [ ] Every added agent passes volume, separation, and distinct-context
- [ ] Roles failing all three implemented as skills instead
- [ ] Exactly one orchestrator
- [ ] A verifier separate from whoever produces the work
- [ ] Models assigned by role, and `command`/`provider`/`model` consistent
- [ ] Each agent started once before skills are installed
- [ ] Objectives retuned per agent → `framework-personalisation`
- [ ] Idle or overlapping agents reviewed and archived

## References

- **`project-discovery`, `framework-personalisation`** (this framework) — the
  steps either side of this one
- **`templates/agent-template.md`** (this repository) — roster entry fields, the
  read-only `identity.md` constraint, and spawn requests
- **`BOOTSTRAP.md`, `QUICKSTART.md`** (this repository) — the install sequence
  this ordering fits into
- **Munder Difflin `PROTOCOL.md`** in the hive root — message verbs and board
  ownership. **The harness wins** over anything written here

**Not sourced — written for this framework:** the three tests, the
self-verification argument, the starting-roster table, the model assignment
table, the creation order, the removal criteria, and the anti-patterns.
