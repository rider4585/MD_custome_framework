# Bootstrap

**Audience:** a **stock `god` agent** that has just been pointed at this
repository and asked to install the framework.

You have **no framework skills yet**. This file assumes nothing — do not try to
invoke a skill until you have installed it. Work through the steps in order and
verify each before moving on.

**What you are doing:** installing the framework, **interviewing the human for
the project's context**, personalising the framework against their answers, and
producing an accurate report of what still needs them.

**You are not starting any project work.** See Step 9.

> ### ⚠️ This framework ships generic, and generic is not usable
>
> The 219 skills describe how to do the work well **in general**. They do not
> know what your project is, what it is for, who it serves, or what its domain
> is. Until you have run the intake interview (Step 6) and personalised against
> the answers (Step 7), every agent you equip will produce work that is
> competent and subtly foreign.
>
> **You must not infer the project from the repository contents.** A repository
> tells you what was built; it does not tell you what it is for, who it is for,
> or what is deliberately out of scope. Inferring is the specific failure this
> sequence exists to prevent. **Ask the human.**

---

## What you can and cannot do

Be clear about this before you start, and do not pretend otherwise in your
report.

| You **can** | You **cannot** |
|---|---|
| Read this repo and run `bin/install-skills.sh` | Enable `orchestratorMaySpawn` — that is a UI setting |
| Install skills into agents that **already exist** in the hive | Create new agents in `roster.json` — UI, or spawn-requests once spawning is on |
| Read `config.json`, `roster.json`, `fleet.json` | Approve anything financial or destructive |
| Write `spawn-requests/*.json` *once spawning is enabled* | Restart an agent so it picks up new skills |

A full self-install is **not possible**. This is a collaborative bootstrap: do
your part, then hand the human a precise list.

---

## Step 0 — Verify the formats still match ⚠️

**Do this before you use any format from this repository.**

Every format here — roster entries, spawn requests, skill locations, message
verbs — was verified by reading a live install of **Munder Difflin v0.4.5** on
**2026-09-06**. That is a **snapshot, not a contract.** The harness is under
active development and its file layout or formats may have changed since.

**The harness ships its own documentation, and it updates with the app:**

```bash
cat "$HARNESS_HOME/hive/PROTOCOL.md"     # messaging, spawning, hive layout
cat "$HARNESS_HOME/hive/COMMANDS.md"     # command reference
ls "$HARNESS_HOME/hive/"                 # what actually exists now
ls "$AGENT_DIR/"                         # your own directory layout
cat "$HARNESS_HOME/roster.json" | head -30
```

**Those files are authoritative. This repository is not.**

### If they disagree

| Do | Do not |
|---|---|
| Follow the **harness's** current format | Apply this repo's format because it is written down |
| **Translate** this framework's content into the new format | Force the old shape and hope |
| Report the drift to the human so the framework can be updated | Silently work around it |

Check these specifically, since the framework depends on them:

- [ ] Are skills still at `agents/<id>/.claude/skills/<name>/SKILL.md`?
- [ ] Is `SKILL.md` frontmatter still `name` / `version` / `description` /
      `allowed-tools`?
- [ ] Is `identity.md` still harness-written and read-only?
- [ ] Are the message `act` verbs still
      `request | inform | propose | query | agree | refuse | done`?
- [ ] Is spawning still `spawn-requests/<id>.json` with `objective` + `cwd`?
- [ ] Does `roster.json` still use the same fields?

**The content is what matters — the 170 skills and 23 agent briefs are
independent of the harness.** If the packaging has changed, repackage it. A skill
is a document; where it lives and what its frontmatter looks like is the
harness's business, not the framework's.

If a format has changed materially, say so in your Step 8 report and do not
guess at the new one — read the harness's own docs, or ask.

---

## Step 1 — Locate everything

You need three paths. Establish them before anything else.

```bash
# 1. This repository — the human told you where it is; confirm it
ls FRAMEWORK_REPO/GOD-PLAYBOOK.md FRAMEWORK_REPO/bin/install-skills.sh

# 2. Your hive root
echo "$AGENT_DIR"                 # .../hive/agents/<your-id>
cat "$HOME/Library/Application Support/munder-difflin/config.json" | grep harnessHome

# 3. Your own agent id
basename "$AGENT_DIR"
```

Record all three. If `$AGENT_DIR` is unset, run `echo $AGENT_DIR` in a fresh
shell — `PROTOCOL.md` in your hive root explains the layout.

**If you cannot find the repo, stop and ask the human for the path.** Do not
guess.

---

## Step 2 — Check whether spawning is enabled

This determines what is possible for the rest of the bootstrap.

```bash
grep -o '"orchestratorMaySpawn"[^,]*' \
  "$HOME/Library/Application Support/munder-difflin/config.json"
```

| Result | Meaning |
|---|---|
| `true` | You can create new agents via `spawn-requests/` |
| `false` | **You cannot spawn.** Requests will sit in the directory unprocessed — they do not fail, they wait |

**If it is `false`, that is not an error and you should not retry.** Record it
for your report; the human must enable it in Settings → Autonomy & Budgets. It
is off by default deliberately, because every worker spends tokens nobody
approved.

Also note `maxConcurrentWorkers` — plans must not assume more parallelism than
that.

---

## Step 3 — Verify the install script works

```bash
cd FRAMEWORK_REPO
./bin/install-skills.sh --list | tail -3      # should report 170 skills
./bin/install-skills.sh --agents              # agents currently in the hive
```

`--agents` is the important one. It prints the **real agent ids**, which carry
generated suffixes when created through the UI:

```
god
jim-mt5tofsm
pam-mt5znwg6
```

**Use these real ids.** The clean names in `agents/*.md` (`backend-engineer`,
`security-lead`) show the intended roster shape — they are not the ids in your
hive unless the human created them that way.

The script only ever writes inside an agent's `.claude/skills/` directory. It
never touches `roster.json`, `identity.md`, `memory.md`, or inboxes.

---

## Step 4 — Install your own skills first

You are the orchestrator; you need routing judgement before you can install
anyone else usefully.

```bash
./bin/install-skills.sh <your-agent-id> \
  project-discovery framework-personalisation agent-roster-design \
  project-context project-client-brand \
  requirements-analysis task-decomposition implementation-plan
```

The first three are the **onboarding pack**, and they are why this step comes
before everything else — they are what turn a generic framework into this
project's framework. The orchestrator is the only agent that needs them.

Verify:

```bash
./bin/install-skills.sh --installed <your-agent-id>
```

> **You will not see these skills until you are restarted.** Claude Code reads
> `.claude/skills/` at session start. Note this in your report — the human
> restarts you, you cannot restart yourself.

Keep your set small. You need judgement about routing and risk, not
implementation detail — the specialists carry that.

---

## Step 5 — Read the playbook

```bash
cat FRAMEWORK_REPO/GOD-PLAYBOOK.md
cat "$HARNESS_HOME/hive/PROTOCOL.md"
```

Read `PACKS.md` too — you will need it in Step 7.

`GOD-PLAYBOOK.md` is the routing authority — where it and your own instructions
disagree, it wins. Before continuing, confirm you have understood:

- **Onboarding is blocking.** Until `project-context` is filled from the human's
  own answers, no agent produces project work
- **Phase 0 is blocking.** Until the applicable `project-*` documents exist and
  contain no `[assumed]` items on anything load-bearing, implementation agents
  are **read-only**
- **The review gate** — the agent that writes a fix **never** verifies it
- **The escalation list** — anything touching money, access, data loss, legal
  consequence, or an architectural decision goes to the human
- **Messaging**: only `request`, `query`, and `propose` expect a reply. Replying
  to an `inform` or `done` loops two agents forever

---

## Step 6 — Ask the human for the project's context ⭐

**This is the step the whole framework is built around. Do not skip it, do not
shorten it, and do not answer any of it yourself.**

Load `project-discovery` and run the interview in it. The rule is **ask, do not
infer** — an orchestrator that guesses the domain by reading the repository will
be plausibly wrong, and every agent downstream inherits the error without ever
seeing the assumption that produced it.

**Send it as one batched message**, grouped by section. A twenty-message
interrogation gets abandoned halfway, and half an intake is worse than none
because it looks finished.

The six sections, in short:

| | Section | Fills |
|---|---|---|
| **A** | What and why — the one-liner, the outcome, done, **non-goals**, deadline | `project-context` |
| **B** | Who — audiences, their real devices, the client, who decides | `project-context`, `project-client-brand` |
| **C** | Domain — industry, compliance, whether it handles money or personal data | **pack selection** |
| **D** | Technical — greenfield/rewrite/extension, stack, hosting, what must not break | `project-context`, `project-architecture` |
| **E** | The work — what is sold, what content exists, what is behind a login | `project-service-catalogue`, `project-content-inventory` |
| **F** | Constraints — what was tried before, what worries them, what is off-limits | `project-context` |

**Push hard on non-goals (A) and on "what has been tried before" (F).** Both are
routinely skipped because they feel awkward to ask, and both surface the
constraint that would otherwise be discovered in week six.

**Write the answers into `project-context` in the same session**, including the
`unknown`s. An answer that stays in a chat log is an answer you have lost.

**If the human cannot answer something:** record `unknown` and what it blocks,
proceed on everything that unknown does not block, and state any assumption you
are forced to make **as an assumption**. Never fill a gap by inference and then
forget you did.

---

## Step 7 — Personalise the framework

Load `framework-personalisation` and run its four passes. None is optional; each
is cheap, and skipping them is what is expensive.

**Pass 1 — Select packs.** Read `PACKS.md`. Install core by lane, then only the
stack and vertical packs the domain answer justifies. **Lean beats complete:**
target twelve skills per agent — twenty is a lot, thirty is a problem. An agent
with sixty skills reads worse at everything, and nothing errors to tell you.

**Pass 2 — Fill project-knowledge.** Every `project-*` skill ships as a template
with a 🟡 banner. Fill them in dependency order:

```
project-context → project-client-brand → project-service-catalogue
  → project-content-inventory → project-architecture / project-site-architecture
    → project-roadmap
```

Remove a 🟡 banner only when the file is genuinely filled. **Delete the
`project-*` templates that do not apply** — an empty `project-database` on a
static site is noise every agent pays for. Keep the ⛔ Phase 0 gate in
`project-client-brand` whatever the domain.

**Pass 3 — Re-example.** The pass everyone skips, and the one that produces most
of the value. These skills follow *principles first, one domain as the worked
example* — the principle is portable, the example is not, and an agent reading a
retail example all day starts to think in retail. Rewrite the examples into this
project's domain. **Read the skill before you change it:** a blind
find-and-replace produces sentences that are grammatical and false, which is the
worst output available.

**Pass 4 — Retune objectives.** The objectives in `agents/*.md` are written
against a generic project. For each agent you instantiate: replace the domain
sentence using `project-context`, set `cwd` and `project`, **write this
project's blocking gates into the objective in capitals** (agents obey
objectives more reliably than they obey documents), cut the paragraphs that do
not apply, and keep the final boundary line naming what the agent must not do.

**Personalise the installed copies in the hive, never the templates in this
repository.** The repository stays generic so the next project can start from
it — that is the entire point of this branch.

---

## Step 8 — Map and equip the existing team

Look at what `--agents` returned. Existing agents usually have generated ids and
a placeholder description like `"a fresh harness"`.

**Do not assume which framework role an existing agent is meant to fill.**
`jim-mt5tofsm` is not necessarily the backend engineer.

**Propose a mapping to the human and wait for confirmation:**

```
Existing agents in the hive:
  jim-mt5tofsm   (claude, cwd .../<repo>, "a fresh harness")
  pam-mt5znwg6   (opencode, cwd .../<repo>, "a fresh harness")

Proposed roles — please confirm or correct:
  jim-mt5tofsm  → backend-engineer   (Claude, on the project repo)
  pam-mt5znwg6  → frontend-engineer  (note: running opencode with a local
                                      model; the framework's skills assume a
                                      capable model — worth reviewing)

I will not install until you confirm.
```

Which roles you need at all is a decision, not a given — load
`agent-roster-design` and start with three or four agents, not the full set of
33. An agent is not free: each costs tokens on every session and adds another
place where context is lost in a handoff.

Once confirmed, install from the agent's file. Each
`agents/<category>/<name>.md` carries a suggested command — **cut it to this
project first** (Step 7, pass 1):

```bash
./bin/install-skills.sh jim-mt5tofsm \
  nodejs javascript express sequelize rest-api error-handling \
  authentication authorization logging configuration backend-testing \
  input-validation transactions concurrency query-optimization \
  project-api project-database project-business-rules
```

Install only for agents that **already exist**. The script refuses unknown
agents and writes nothing — that is correct behaviour, not a failure to work
around.

---

## Step 9 — Stop. Do not start project work.

Phase 0 has not run. The `project-*` documents hold intake answers, not verified
detail about the existing system or the client's real rules.

**Any code or copy written now would be guessing at conventions** — which
produces plausible output that quietly violates the system, or confident claims
about a client that turn out to be wrong. That is the failure this framework
exists to prevent.

What you may do now: plan, read, review, and prepare. What you may not do:
implement or publish.

---

## Step 10 — Report to the human

Produce a report in this shape. Be accurate about what is blocked — an
optimistic report is worse than none.

```markdown
## Framework bootstrap — status

**Done**
- Located framework repo, hive root, and my agent id
- Installed the onboarding pack into <my-id>
- Read GOD-PLAYBOOK.md, PACKS.md, and PROTOCOL.md
- Ran the project intake interview
- Filled project-context: <n> fields answered, <n> unknown
- Personalised: <packs installed, per agent>; re-exampled <n> skills;
  retuned <n> agent objectives
- Deleted these inapplicable project-* templates: <list>

**Needs you**
1. Restart me so I pick up my new skills (I cannot restart myself)
2. Enable `orchestratorMaySpawn` in Settings → Autonomy & Budgets
   — currently <true/false>. Without it I cannot create agents.
3. Confirm the role mapping for existing agents: <proposal>
4. Answer the open questions blocking work: <the batched list>
5. Create these missing agents: <only the ones needed next, not all 33>

**Still unknown, and what it blocks**
<the open-questions table from project-context>

**Left deliberately generic**
<skills I did not re-example, and why>

**Not done / uncertain**
<anything you could not verify — say so plainly>
```

**"Still unknown" and "left deliberately generic" are the most useful sections
in this report.** They are what stop a human assuming the framework knows more
about their project than it does.

---

## Step 11 — When cleared, begin Phase 0

Only after the human has restarted you, enabled spawning, confirmed the mapping,
and answered the blocking questions.

Follow `GOD-PLAYBOOK.md`. In short: spawn the discovery roles read-only, have
them produce the `project-*` documents for whatever already exists, and **batch
every `[assumed]` question into a single ask**. Trickling questions out one at a
time is how this phase fails in practice.

Phase 0 exits when the applicable `project-*` documents contain **zero
`[assumed]` items** on anything load-bearing — money, access, data integrity,
legal claims, or published facts about the client. Not before.

---

## If something does not work

| Symptom | Cause |
|---|---|
| `could not determine harnessHome` | Set `MUNDER_HARNESS_HOME` to the hive root |
| `agent "x" not found in the hive` | Use the real suffixed id from `--agents` |
| Skills installed but I cannot see them | You have not been restarted |
| Spawn request sits in `spawn-requests/` | `orchestratorMaySpawn` is off — Step 2. Do not retry; raise it |
| An agent produces competent but contextually wrong work | Step 7 was skipped or done shallowly. Re-run passes 3 and 4 |
| Agents keep asking the human the same question | It belongs in `project-context` and was never written down |
| Every project ends up looking like the last one | You personalised the repository instead of the hive copies |
| `Circuit breaker` message in your inbox | **You are the problem it caught.** Stop repeating, summarise what you tried, and do exactly what the message says |

Do not work around a refusal. If the script or the harness declines something,
that is usually a guard doing its job — report it rather than routing around it.
