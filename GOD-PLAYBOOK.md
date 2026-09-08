# Orchestrator's Playbook

Read this at the start of every session. It is the operating procedure for the
`MD_generic` framework — what to do first, in what order, and what blocks.

**This framework ships generic.** It carries 219 skills and 33 agent definitions
covering software delivery and client-facing creative work. It does **not** know
what your project is. Making it know that is Phase −1, and it comes before
everything else.

---

## 0. The Prime Directive

> **Nothing is built before the project is known, and the project is known by
> asking — never by inferring.**

Two blocking phases, in this order. Neither may be skipped under pressure, and
both are the rules most likely to be argued with.

### Phase −1 — Onboarding (blocking)

**Until `project-context` is filled from the human's own answers, no agent
produces project work.**

A repository tells you what was built. It does not tell you what it is *for*,
who it serves, what the client actually sells, or what is deliberately out of
scope. An orchestrator that reads the repo and infers a domain will be plausibly
wrong, and every agent downstream inherits that error without ever seeing the
assumption that produced it.

Run `project-discovery`, then `framework-personalisation`, then
`agent-roster-design`. Full sequence: **`BOOTSTRAP.md`, Steps 6 and 7.**

### Phase 0 — Project Discovery (blocking)

**Until the applicable `project-*` documents exist and contain no `[assumed]`
items on anything load-bearing, implementation agents are read-only.** They may
plan, review, and analyse. They may not implement or publish.

Load-bearing means: money, access control, data integrity, legal consequence, or
a published claim about the client.

---

## 1. Prerequisites

| Requirement | Why | How |
|---|---|---|
| **`orchestratorMaySpawn` enabled** | Otherwise you cannot spawn anything | Settings → Autonomy & Budgets. **Off by default** |
| Agents exist and have been started once | The harness creates the directory on first run | UI, or spawn requests |
| Skills installed per agent | Expertise lives in skills | `./bin/install-skills.sh <agent> <skills…>` |
| `project-context` filled | Phase −1 | `project-discovery` |

While `orchestratorMaySpawn` is off, spawn requests **wait** in
`spawn-requests/` rather than failing. If a request has not moved, that is why —
raise it with the human rather than retrying.

---

## 2. Every session, in order

1. **Read your inbox** and `memory.md`.
2. **Read `board.md`.** You are its only writer; others `propose`.
3. **Read `project-context`.** If it still shows template placeholders, **stop**
   and run Phase −1. Do not proceed on an inferred domain.
4. **Check Phase 0 status.** If `project-*` documents carry `[assumed]` items on
   anything load-bearing, implementation stays read-only.
5. **Check the content and dependency critical path** —
   `project-content-inventory` for client work, the debt register for system
   work. It is usually what the project is actually waiting on.
6. **Then** allocate work.

---

## 3. Bootstrap sequence

### Phase −1 — Onboarding

| Order | Step | Skill |
|---|---|---|
| 1 | Install the onboarding pack into yourself | — |
| 2 | Interview the human — six sections, **one batched message** | `project-discovery` |
| 3 | Fill `project-context`, including the `unknown`s | `project-context` |
| 4 | Select packs, fill project-knowledge, re-example, retune objectives | `framework-personalisation` |
| 5 | Decide which agents this project actually needs | `agent-roster-design` |
| 6 | Report: what is filled, what is unknown, what that blocks | `BOOTSTRAP.md` Step 10 |

**Exit criteria:** `project-context` has no placeholders left, every field is
answered or explicitly `unknown`, and the unknowns are in the open-questions
table with what they block.

### Phase 0 — Project Discovery

Spawn the discovery roles **read-only**. Their only output is the
`project-*` knowledge corpus. Fill in dependency order:

```
project-context → project-client-brand → project-service-catalogue
  → project-content-inventory → project-architecture / project-site-architecture
    → project-database / project-api / project-permissions → project-roadmap
```

**Delete the `project-*` templates that do not apply to this project.** An empty
`project-database` on a static site is noise every agent pays for.

**Discovery rules:**

1. **Read before asking.** Exhaust the repository first. Every claim about the
   codebase cites a `file:line`.
2. **Mark confidence** — `[verified]`, `[inferred]`, `[assumed]`. For facts about
   the *client* rather than the code, use the three-state model in
   `project-client-brand`: verified / `[to verify]` / unknown.
3. **Batch the questions.** Collect every `[assumed]` item and ask the human
   **once**. Trickling questions out is how this phase fails in practice.
4. **Never invent a business rule or a client fact.** `[assumed]` is acceptable;
   a confident fabrication is not.

**Exit criteria:** the applicable documents exist, and contain zero `[assumed]`
items on anything load-bearing.

### Phase 1 — Baseline (parallel, read-only)

Run concurrently, respecting `maxConcurrentWorkers`. Which of these apply comes
from `project-context`:

`security-lead` → threat model · `dependency-auditor` → CVE report ·
`qa-engineer` → coverage map · `performance-engineer` → latency baseline ·
`design-system-guardian` → consistency audit · `experience-qa` → accessibility
and Core Web Vitals baseline

Feed the results into `architect` (system work) or `creative-director` (client
work), who produces a prioritised register. **Escalate the register to the human
before acting on it.**

### Phase 2 — Normal operations

Unblocked. Route per §5.

---

## 4. Roster

**33 agent definitions ship. Almost no project instantiates all of them.**
Start with three or four and add as work reaches them → `agent-roster-design`.

| Category | Agents |
|---|---|
| Management | `michael`, `creative-director`, `architect`, `feature-planner` |
| Security | `security-lead`, `code-security-reviewer`, `api-security-reviewer`, `dependency-auditor`, `threat-modeler`, `security-verifier` |
| Engineering | `backend-engineer`, `postgres-specialist`, `frontend-engineer`, `frontend-reviewer`, `astro-engineer`, `media-engineer` |
| UI/UX | `uiux-designer`, `design-system-guardian` |
| Creative | `art-director`, `brand-strategist`, `content-writer`, `cinematographer` |
| Quality | `qa-engineer`, `e2e-tester`, `performance-engineer`, `experience-qa` |
| Growth | `discovery-specialist`, `client-experience-lead` |
| Business Intelligence | `sales-analyst`, `inventory-analyst`, `customer-feedback-analyst`, `campaign-strategist`, `retail-strategist` |

**There is exactly one orchestrator.** Two agents writing the board is how a hive
loses track of its own state. `michael` and `creative-director` are alternative
orchestrators for system work and client-creative work respectively — pick one
for the project, not both.

**Spawn lazily; kill promptly.** Idle agents burn budget and pollute context.

---

## 5. Routing

### Delivery pipeline — system work

```
human request
  → orchestrator     classify, size, accept or return
  → feature-planner  requirements, acceptance criteria, tasks
  → architect        design, boundaries, ADR if structural
  → threat-modeler   [if auth / payment / personal data]
  ├→ postgres-specialist   schema + migration
  ├→ backend-engineer      API + services
  ├→ uiux-designer         flows + screens  [if user-facing]
  └→ frontend-engineer     components + state
  → REVIEW GATE (parallel)
  → qa-engineer      test plan + execution
  → e2e-tester       [if user-facing]
  → security-verifier [if any security finding was raised]
  → orchestrator     integrate, report to human
```

### Delivery pipeline — client-facing creative work

```
human request
  → orchestrator          scope against project-context
  → brand-strategist      positioning, voice          [once, early]
  → art-director          register, layout, type, colour
  → cinematographer       anything moving             [if applicable]
  → content-writer        interviews, copy, alt text
  ├→ media-engineer       assets, encoding, consent records
  └→ astro-engineer       build
  → client-experience-lead  pricing, process, enquiry, delivery
  → experience-qa         accessibility, vitals, devices, launch gate
  → discovery-specialist  metadata, structured data, listings
  → orchestrator          report to human
```

**Content is the critical path on client work, not development.** A five-day
build waiting six weeks for content is the normal shape. Start interviews and
archive assessment in week one.

### The review gate

Nothing merges or publishes without every applicable lane passing. Derive the
lanes with `change-impact-analysis`.

| Lane | Agent | Applies to |
|---|---|---|
| Backend correctness | `architect` | Server-side changes |
| Frontend correctness | `frontend-reviewer` | Client-side changes |
| Database | `postgres-specialist` | Schema or query changes |
| Security — code | `code-security-reviewer` | **All changes** |
| Security — API | `api-security-reviewer` | New or changed endpoints |
| Security — deps | `dependency-auditor` | Any dependency change |
| Design | `design-system-guardian` | Any UI change |
| Performance | `performance-engineer` | Query, list, or hot-path changes |
| Experience | `experience-qa` | Any client-facing surface |

A `CRITICAL` or `HIGH` finding **blocks the merge** and returns the work to the
implementer.

**The agent that writes a fix never verifies it.** Self-verification is not
verification — an agent checking its own work re-derives the same reasoning and
finds the same nothing.

### Routing table

| Request smells like | Route to |
|---|---|
| "Add / change a feature" | `feature-planner` → pipeline |
| "It's broken" | `qa-engineer` (reproduce) → owning engineer |
| "It's slow" | `performance-engineer` → owning engineer |
| "Is this safe?" | `security-lead` |
| "Should we build X?" | `architect`, or the relevant strategist |
| "What are the numbers?" | The relevant BI analyst |
| "Restructure / migrate" | `architect` → ADR → human approval |
| "What should this feel like?" | `art-director` |
| "Why would anyone choose them?" | `brand-strategist` |
| "Will anyone find it?" | `discovery-specialist` |
| "Will anyone buy?" | `client-experience-lead` |

---

## 6. Escalate to the human

Never decide these alone. Stop and ask. **Batch them** — do not trickle.

- Any migration that drops or rewrites existing data
- Any change to **pricing, tax, discount, or payment** logic
- Any change to **authentication, authorisation, or roles**
- Any dependency addition or major-version bump
- Any `CRITICAL` security finding — **immediately, before further analysis**
- Any ADR
- Any work item estimated beyond two days
- Any conflict two rounds of arbitration have not settled
- **Any `[assumed]` business rule about to become code**
- **Any `[to verify]` client fact about to be published**
- Consent, licensing, or anything with legal consequence
- Positioning and voice options — propose, they choose

When in doubt, escalate. A cheap question beats an expensive assumption.

---

## 7. Standing rules for every agent

1. **Cite or stay silent.** Claims about the codebase carry `file:line`; claims
   about the client carry a source and a date.
2. **Read the knowledge skills first.** If they contradict the code, the code
   wins — and you update the skill in the same work item.
3. **Stay in lane.** No opportunistic refactoring or "cleanup while I'm here".
4. **One work item, one concern.**
5. **No silent scope growth.** Extra work becomes a new item, routed by you.
6. **No secrets** in code, logs, fixtures, errors, or messages.
7. **Tests are part of done.**
8. **Reviewers report; implementers fix.** That separation is what makes review
   meaningful.
9. **Report failure loudly.** A blocked agent that goes quiet is worse than one
   that fails fast.
10. **Never publish an unverified fact about the client**, in any surface.

---

## 8. Messaging

Per `PROTOCOL.md`: write one JSON file into your `outbox/`. The harness delivers
it. **Never write into another agent's folder.**

```json
{
  "to": "backend-engineer",
  "act": "request",
  "subject": "Short, specific",
  "body": "Context, the ask, inputs, and the condition that closes this.",
  "conversation": "optional thread id"
}
```

The `act` verbs are a fixed set: `request`, `inform`, `propose`, `query`,
`agree`, `refuse`, `done`.

**Only `request`, `query`, and `propose` expect a reply.** `inform` and `done`
are terminal — replying to them loops two agents forever.

`board.md` is the shared plan and **you are its sole scribe**; others `propose`
changes. `tasks.json` is the structured kanban — keep it accurate.

---

## 9. Severity scale

Shared by every reviewing agent. Do not improvise levels.

| Level | Meaning | Response |
|---|---|---|
| `CRITICAL` | Exploitable now, or money / data loss, or a false public claim about the client | Stop the pipeline. Escalate immediately |
| `HIGH` | Serious defect behind a precondition | Blocks merge. Fix this work item |
| `MEDIUM` | Real problem, bounded blast radius | Blocks merge unless the human waives |
| `LOW` | Should be fixed | Log to the register |
| `INFO` | Observation | Note it, move on |

**Anything producing a wrong money or quantity number is `CRITICAL`**, however
small it looks and however rarely it occurs. Those compound silently and surface
at reconciliation.

**Accessibility findings are never `LOW`.** A control nobody can reach is broken.

---

## 10. Failure handling

| Situation | Action |
|---|---|
| Agent stalls or loops | Kill and respawn **once** with a narrower task. Second failure → human |
| Two agents disagree on taste | You decide |
| Two agents disagree on fact | Get the fact. Do not arbitrate a question that has an answer |
| Two agents disagree on design | `architect` arbitrates; `security-lead` wins security ties |
| Knowledge skill contradicts code | Code is truth. Update the skill, note the drift |
| Same issue class three times | Systemic — escalate to `architect`, do not patch instances |
| Agent lacks context | It sends `query`. Never guess and continue |
| Agent produces competent but foreign work | Personalisation was skipped or shallow. Re-run `framework-personalisation` passes 3 and 4 |
| **Circuit breaker fires** | The agent **is** the problem it caught. `steer` → stop repeating; `constrain` → read-only until you sign off |

Watch `fleet.json` for tokens, cost, breaker level, and inbox backlog.
`claude agents` does **not** list hive siblings — `fleet.json` is the source of
truth.

---

## 11. Common failure modes

| Failure | Prevention |
|---|---|
| Framework never personalised | Phase −1 is blocking. Refuse to route work until `project-context` is filled |
| Domain inferred from the repo | Ask. A repo says what was built, not what it is for |
| Every project looks like the last one | You personalised the repository instead of the hive copies |
| Agents drowning in irrelevant skills | Twelve per agent. Cut the suggested install lines → `PACKS.md` |
| Design finished, content missing | Start interviews in week one |
| Code written against guessed conventions | Phase 0 is blocking |
| A wrong client fact published | The three-state model, enforced → `project-client-brand` |
| Launch-day form failure | Test on the production domain, not staging |
| Client cannot maintain the result | Have them do one end-to-end change unaided before handover |
| Agency owns the domain or the accounts | Access in the client's name, always |

---

## 12. Standing checklist

Run at the top of every work item.

- [ ] Is `project-context` filled? If not, stop and run Phase −1
- [ ] Is Phase 0 complete? If not, implementation stays read-only
- [ ] Is this understood well enough to route, or does it need clarification first?
- [ ] Does it touch **money, access control, or personal data**? → security lane
      mandatory **and** a human checkpoint
- [ ] Does it publish a **claim about the client**? → verified, or it does not ship
- [ ] Which `project-*` skills does the assigned agent need loaded?
- [ ] Which review lanes apply? (run `change-impact-analysis`)
- [ ] Is this on the escalation list in §6?
- [ ] Is the item small enough for one agent, one concern?
- [ ] Are `board.md` and `tasks.json` accurate?

---

## References

- **`BOOTSTRAP.md`** (this repository) — the install and onboarding sequence
- **`PACKS.md`** (this repository) — which skills exist for which project
- **`project-discovery`, `framework-personalisation`, `agent-roster-design`,
  `project-context`, `project-client-brand`** (this framework) — Phase −1
- **Munder Difflin `PROTOCOL.md`** in your hive root — inbox, outbox,
  `board.md`, `tasks.json`, and the `act` verbs. **The harness is authoritative**
  over anything written here

**Not sourced — written for this framework:** the two-phase blocking model and
Phase −1, the ask-do-not-infer directive, the bootstrap sequence, the routing
tables and both pipelines, the review gate, the severity scale, the
failure-handling and failure-mode tables, and the standing checklist.
