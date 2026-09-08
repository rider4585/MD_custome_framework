# Quickstart

Setting up the `MD_creative_image_photography` framework in a Munder Difflin
hive. About 30
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

Set `cwd` to **the photography site repository**, not this framework repository.

Start with three and add the rest as work reaches them:

| First | Then |
|---|---|
| `creative-director` | `content-writer`, `cinematographer` |
| `brand-strategist` | `media-engineer`, `client-experience-lead` |
| `art-director` | `astro-engineer`, `experience-qa`, `discovery-specialist` |

Add `cinematographer` early if film is a real product — the music licence audit
is the finding you least want arriving in launch week.

**Start each agent once** so the harness creates its directory. `install-skills.sh`
refuses to write into an agent that has never run.

## 3. Install the skills

```bash
cd "/path/to/Munder Difflin Framework"
git checkout MD_creative_image_photography

./bin/install-skills.sh --agents                    # confirm they exist
./bin/install-skills.sh --dry-run creative-director \
  project-photographer-brand signature-style emotional-brief
```

Then run the install command from the `## Skills` section of each agent file:

```bash
./bin/install-skills.sh creative-director \
  project-photographer-brand project-shoot-catalogue project-content-inventory \
  project-site-architecture signature-style emotional-brief brand-narrative \
  wedding-story-arc case-study-structure media-consent launch-review
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

**This is blocking by design.** Exactly one fact about this client is verified —
the Instagram handle `@creative_weddings_films_latur`. The grid could not be read
(Instagram login-walls automated fetches) and no public directory listing
corroborates the business.

Before any design or engineering work:

- [ ] Confirm the trading name, its exact spelling, and the location
- [ ] Answer the twenty-seven unknowns in `project-photographer-brand`
- [ ] **Resolve stills-vs-film** — is film a real product? It changes the
      navigation, the packages page, and the case-study shape
- [ ] Fill the Instagram gap — a human with account access must supply the bio,
      a post breakdown, and **100+ frames the photographer chose themselves**
- [ ] Locate and assess the archive, including whether RAWs are kept
- [ ] Audit music licences for every published reel and film
- [ ] Confirm the shoot catalogue and service list
- [ ] Get packages, prices, travel rule, and turnaround times — or publish none

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
