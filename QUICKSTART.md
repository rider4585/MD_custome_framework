# Quickstart

Setting up the `MD_eventina` framework in a Munder Difflin hive. About 30
minutes, most of it waiting for agents to start.

---

## 0. Verify the formats first

**Do this before anything else.** The formats in this repository were verified
against Munder Difflin v0.4.5 on 2026-09-06. If the app has moved on, the harness
wins.

```bash
export HARNESS_HOME="$(python3 -c "import json,os;print(json.load(open(os.path.expanduser('~/Library/Application Support/munder-difflin/config.json')))['harnessHome'])")"
echo "$HARNESS_HOME"

cat "$HARNESS_HOME/hive/PROTOCOL.md" | head -60
head -30 "$HARNESS_HOME/roster.json"
ls "$HARNESS_HOME/hive/agents/"
ls "$HARNESS_HOME/hive/agents/god/.claude/skills/" 2>/dev/null
```

Check three things:

- [ ] Skills still live at `agents/<id>/.claude/skills/<name>/SKILL.md`
- [ ] `roster.json` still has `id`, `name`, `character`, `description`, `cwd`,
      `command`, `provider`, `model`
- [ ] Message `act` verbs are still `request | inform | propose | query | agree |
      refuse | done`

**If any differ, translate this framework's content into the current format
rather than forcing the shape written here** — then fix `templates/`.

## 1. Enable spawning

Munder Difflin ships with `orchestratorMaySpawn` **off**. Without it the creative
director cannot create agents, and spawn requests sit in the directory rather
than failing visibly.

**Settings → Autonomy & Budgets → enable `orchestratorMaySpawn`.**

## 2. Create the agents

Agent creation is a UI action — it cannot be scripted from here. For each file in
`agents/`, create an agent with the `id`, `name`, `character`, `accent`,
`description`, `command`, `provider`, and `model` from its roster entry block.

Set `cwd` to **the Eventina site repository**, not this framework repository.

Start with three and add the rest as work reaches them:

| First | Then |
|---|---|
| `creative-director` | `content-writer` |
| `brand-strategist` | `media-engineer` |
| `art-director` | `astro-engineer`, `experience-qa`, `discovery-specialist` |

**Start each agent once** so the harness creates its directory. `install-skills.sh`
refuses to write into an agent that has never run.

## 3. Install the skills

```bash
cd "/path/to/Munder Difflin Framework"
git checkout MD_eventina

./bin/install-skills.sh --agents                    # confirm they exist
./bin/install-skills.sh --dry-run creative-director \
  project-eventina-brand emotional-brief brand-narrative
```

Then run the install command from the `## Skills` section of each agent file:

```bash
./bin/install-skills.sh creative-director \
  project-eventina-brand project-event-catalogue project-content-inventory \
  project-site-architecture emotional-brief brand-narrative \
  case-study-structure media-consent performance-budget launch-review
```

**Restart each agent** afterwards so Claude Code picks up the new skills.

Verify:

```bash
./bin/install-skills.sh --installed creative-director
```

## 4. Point the director at the playbook

In the creative director's session:

> Read `GOD-PLAYBOOK.md` in the framework repo at `<path>` and work through it.

## 5. Run Phase 0 before anything else

**This is blocking by design.** No fact about Eventina has been confirmed by the
client, and the Instagram grid could not be read.

Before any design or engineering work:

- [ ] Confirm the eight `[to verify]` facts in `project-eventina-brand`
- [ ] Answer the fourteen unknowns in the same file
- [ ] Fill the Instagram gap — a human with account access must supply the bio,
      post breakdown, and best-performing posts
- [ ] Locate and assess the photograph archive
- [ ] Confirm the event catalogue and service list

Until that is done, agents may plan but must not publish anything containing a
`[to verify]` value.

## Troubleshooting

| Symptom | Cause |
|---|---|
| `install-skills.sh` says the agent is not found | It has not started once. Start it, then retry |
| `config.json not found` | Set `MUNDER_HARNESS_HOME` to your hive root |
| Skills installed but the agent ignores them | Restart the agent |
| Director cannot spawn | `orchestratorMaySpawn` is still off |
| Skill name collision | The namespace is flat across the whole hive. Check for a same-named skill from another branch |

## References

- **Munder Difflin `PROTOCOL.md` and `COMMANDS.md`** in your hive root — the
  authoritative, version-current formats
- **`templates/agent-template.md`** and **`templates/skill-template.md`** in this
  repository — the snapshot these instructions assume

**Not sourced — written for this framework:** the setup sequence, the agent
ordering, the Phase 0 gate, and the troubleshooting table.
