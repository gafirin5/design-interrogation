# Design Interrogation

An Agent Skill that turns a half-formed idea into a set of decisions you can defend to someone who wasn't in the room.

Most planning tools help you *write down* a plan. This one helps you *find out* whether you have one. It interviews you in dependency-ordered waves, attacks what you settled, and refuses to declare victory until every branch is closed.

## What it does

You bring a vague idea — a feature, a migration, a business call, a piece of writing. The skill treats it as a **decision tree**:

- Each decision is a **node**
- Each node has prerequisites — an **edge**
- The **frontier** is the set of nodes whose parents are all settled: the questions answerable *right now*

It asks the whole frontier as **one wave**, each question carrying a recommendation. You answer, the frontier pushes outward, and the next wave arrives. Never one question at a time trickling for an hour.

Once the tree narrows, it stops extending and starts attacking: red-team waves, pre-mortems, assumption audits, contradiction sweeps.

## Why not just ask an LLM to interview me?

Because a generic interview has three failure modes that this skill is built to prevent:

| Failure | What the skill does |
|---|---|
| **Queue instead of wave** | Asks one question at a time, blocking on each | Asks the entire frontier at once |
| **Premature children** | Asks questions whose prerequisites are unanswered | Refuses to ask a node with an open parent |
| **Theatre** | Produces 40 impressive questions you rubber-stamp | Enforces ranking, importance tags, and a disagreement signal |

It also splits ownership explicitly: **facts are the agent's job** (it dispatches exploration subagents), **decisions are yours**. It will not ask you something it could look up.

## Profiles

Rigor is matched to reversibility, not to enthusiasm.

| Profile | Waves | Adversarial pass | Risk scoring | Use for |
|---|---|---|---|---|
| **Quick** | 1–2 | — | — | 10-minute sanity check |
| **Standard** | 3–5 | light red-team | — | Most features, plans |
| **Deep** | 5–8 | full red-team, pre-mortem | yes | Architecture, migrations |
| **Exhaustive** | until empty | all five passes + assumption audit | yes + ranking | One-way doors |

## Files

```
SKILL.md                          Orchestrator — loop, rules, wave format
agents/openai.yaml                Interface metadata
references/
├── decision-tree-model.md        Nodes, edges, frontier, pruning, splitting
├── profiles.md                   Per-profile checklists and intensity
├── question-craft.md             Seven archetypes, phrasing, anti-leading
├── adversarial-passes.md         Red-team library, pre-mortem, audits
├── scoring.md                    Importance / reversibility / confidence
├── closure.md                    Six-part closure gate
├── ledger-and-handoff.md         Ledger schema, conflict detection, handoff
└── worked-example.md             Full 4-wave session, start to finish
```

`SKILL.md` lazy-loads references — only the files the current profile needs.

## Install

### QwenPaw

```bash
git clone https://github.com/gafirin5/design-interrogation.git \
  ~/.qwenpaw/workspaces/default/skills/design-interrogation
qwenpaw skills enable design-interrogation --agent-id default
qwenpaw skills test design-interrogation --agent-id default
```

Or via Console: **Workspace → Skills → Add Skill → Upload via Zip**.

### Claude Code / other agents

Copy the directory into your skills folder. The entry point is `SKILL.md`; `name` and `description` are in the YAML front matter.

## Usage

Trigger it explicitly, or let the description match:

```
bedah ide — mau bikin tool rekonsiliasi CSV mingguan dari bank
```

Indonesian triggers: `bedah`, `uji ide`, `interrogasi desain`.
English: `stress-test`, `pressure-test my thinking`.

Start in a **fresh conversation**, not on top of a plan an agent already wrote for you.

## Status

`version: "2.0"` — no code, no dependencies, no network calls. The skill is pure instructions, so it works in any agent that reads `SKILL.md`.

Stateless by design: it writes no files during a session unless you ask for the ledger (Exhaustive profile only).

## License

MIT
