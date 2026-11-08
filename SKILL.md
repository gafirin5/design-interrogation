---
name: design-interrogation
description: Stress-test a half-formed idea by walking its decision tree in dependency-ordered waves until every branch is settled. Supports depth profiles, adversarial passes, risk scoring, and assumption audits. Use when the user wants their thinking pressure-tested, says "bedah", "uji ide", "stress-test", "interrogasi desain", or wants to think a plan through before building it.
metadata:
  version: "2.0"
  requires: {}
---

# Design Interrogation

Turn a vague idea into a set of decisions the user can defend to someone who wasn't in the room.

You are not writing a plan. You are extracting one from the user's head — one dependency layer at a time — and then attacking it to find where it breaks.

**Core loop in one line:** compute the frontier → ask the whole frontier in one wave → halt → absorb → repeat until empty → audit → confirm.

---

## 1. Operating rules (non-negotiable)

| Rule | Meaning |
|---|---|
| **Wave, never queue** | Ask the entire frontier at once. Never one question at a time unless the profile says so. |
| **Halt after every wave** | No follow-up wave, no partial action, no "I'll assume…" in the same turn. |
| **No premature children** | Never ask a node whose parent is still open. It isn't hard, it's early. |
| **Facts are yours, decisions are theirs** | Look up anything discoverable. Put every real choice to the user. |
| **Recommendation on every question** | Never a bare question. Always "→ Recommended: X because Y." |
| **"Yes" means accept** | Phrase so one word agrees with your recommendation. |
| **No action until confirmation** | Do not build, write code, or produce a spec before explicit sign-off. |

Breaking any of these silently is worse than a slow session, because the user cannot see the breach.

---

## 2. The model: nodes, edges, frontier, depth

Treat the idea as a **decision tree**.

* **Node** — one open question, with an ID (`N1`, `N2.3`, …).
* **Edge** — a prerequisite. Node B inherits its prerequisite from A.
* **Depth** — how many edges from root. Depth 0 = framing, depth 1 = shape, depth 2 = mechanics, depth 3 = edge cases.
* **Frontier** — all nodes whose parents are settled. Answerable *now*.
* **Blocked** — parents unresolved. Future waves.
* **Settled** — decided, with the decision recorded verbatim.

Read `references/decision-tree-model.md` for how to build and prune the tree, including root discovery, branching heuristics, and how to detect that you've collapsed two distinct nodes into one.

---

## 3. Profiles: choose the interrogation intensity first

Different ideas deserve different rigor. **Before wave 1**, pick a profile — or infer it and state the inference so the user can override.

| Profile | Waves | Questions/wave | Adversarial pass | Risk scoring | Use when |
|---|---|---|---|---|---|
| **Quick** | 1–2 | 3–5 | no | no | Throwaway idea, 10-minute sanity check |
| **Standard** | 3–5 | 5–9 | yes, light | no | Default. Most features, plans, decisions |
| **Deep** | 5–8 | 6–12 | full red-team | yes | Architecture, irreversible calls, anything expensive to undo |
| **Exhaustive** | until empty, no cap | full frontier | full + assumption audit + pre-mortem | yes + ranking | High-stakes, one-way-door decisions |

State it in one line: `Profile: Deep (inferred — you said "migrasi database produksi"). Override with: quick/standard/deep/exhaustive.`

Read `references/profiles.md` for the per-profile checklists and what each adds.

---

## 4. The loop

1. **Frame.** Restate the idea in one sentence. If you cannot, that's node N0 and it's the only question this wave.
2. **Build.** Expand the tree to the current frontier. Do not expand past blocked nodes.
3. **Score.** For each frontier node, assign importance (1–5) and reversibility (easy / hard / one-way-door). Drop nodes scoring ≤2 with easy reversibility — those are details you're allowed to decide later.
4. **Emit the wave** in the format of §5.
5. **Halt.** End the turn.
6. **Absorb.** Record each answer verbatim in the ledger (§6). If an answer conflicts with a settled node, raise the conflict *immediately* as the first item of the next wave.
7. **Recompute.** Push the frontier outward. Repeat from step 3.

A question whose answer depends on another still-open node in this wave belongs to a **later** wave, not this one. This is the most common violation — check for it every wave.

---

## 5. Wave format

```
🌊 Wave 2 / ~Deep — 7 open decisions
Frontier: N3, N4, N7, N8, N9, N11, N12   Blocked: N5, N10 (waiting on N3, N4)

**Q1 · N3 — <short title>**  `importance 5 · one-way-door`
<Body. Name the tension. Give 2–4 concrete options with the real tradeoff of each. 1–3 short paragraphs.>

→ Recommended: <answer> — <one-line reason>

---

**Q2 · N4 — <short title>**  `importance 3 · easy to reverse`
...
```

Footer on every wave:

```
Answers: reply per number, or "ok" to accept all recommendations.
Blocked this round: N5, N10 — they open once N3/N4 land.
```

Rules on wording:
* Name options concretely. "Session cookies / JWT in localStorage / server-side with sticky sessions" is answerable; "how should auth work?" is not.
* Attach a tag to each question: importance + reversibility. This tells the user where to spend attention.

Read `references/question-craft.md` for phrasing patterns, the seven question archetypes, and how to write a body that doesn't lead the witness.

---

## 6. The ledger

You must maintain a session ledger even though the skill is stateless on disk.

At the top of **every wave**, include a compressed recap:

```
📋 Settled so far (4)
N1 Idea: import CSV → reconcile → export, weekly.   N2 User: single accountant, no dev.
N3 Storage: Postgres, not SQLite (concurrency).      N4 Output: XLSX, not Dashboard.
```

If the conversation is long, dump the ledger to a file the user can keep (`interview-ledger.md`) — but only when the user asks or the profile is Exhaustive. Never write files by default.

Read `references/ledger-and-handoff.md` for ledger schema, conflict detection rules, and the handoff format for passing a finished interrogation into a spec or build step.

---

## 7. Facts versus decisions

**Facts are yours.** Filesystem contents, a library's real API, whether something is deployed, what the current config says — go find it.

Dispatch a subagent with a fresh context for exploration. **Do not block the wave on it.** While it runs, its downstream nodes are still blocked: omit them, ask the rest of the frontier now, and let them join a later wave when facts land.

**Decisions are the user's.** Every genuine choice gets put to them explicitly, with a recommendation, and you wait.

A fact you could have looked up but asked the user for is a **trust leak** — it signals you're guessing.

---

## 8. Adversarial passes

After the frontier narrows (or at the profile's designated point), attack the settled tree instead of extending it. Run the passes in `references/adversarial-passes.md`:

1. **Red-team wave** — one wave whose questions are all "how does this fail?"
2. **Pre-mortem** — "it's six months later and this failed. What killed it?"
3. **Assumption audit** — list every implicit premise; each becomes a node.
4. **Contradiction sweep** — find two settled nodes that cannot both be true.
5. **Second-order effects** — what does this make easier, harder, or unnecessary?

Use the reference file's attack library rather than inventing generic objections.

---

## 9. Scoring and ranking

For Deep and Exhaustive, maintain a risk register. Each settled decision carries:

| Field | Values |
|---|---|
| Importance | 1–5 |
| Reversibility | easy / hard / one-way-door |
| Confidence | user knew / user guessed / we assumed |
| Blast radius | local / team / org / external |
| Residual risk | none / mitigated / accepted / unknown |

Rank one-way-door + low-confidence + high-blast-radius first; those are where the interrogation was too thin and the session should loop back.

Read `references/scoring.md` for the rubric and the closing risk table format.

---

## 10. Ending

The tree is exhausted when every branch is visited, every "I don't know" is converted into a prototype, a deferral with a date, or an explicit risk, and the closure checklist passes.

**Closure checklist** (`references/closure.md`):
- [ ] No node has an open parent
- [ ] No wave introduced an option left undecided
- [ ] Every "don't know" → prototype / dated deferral / accepted risk
- [ ] No two settled decisions contradict
- [ ] Every one-way-door has an explicit confidence rating
- [ ] Ungrillable questions were identified and routed to a prototype, not talked to death

Then: summarise settled decisions compactly, show the risk table, and ask for explicit confirmation that you understand the idea the same way the user does. **Only an explicit yes ends it.**

After confirmation, read `references/ledger-and-handoff.md` for the handoff block the user can paste into a spec, a ticket, or a build step.

---

## 11. Failure modes

| Failure | Symptom | Fix |
|---|---|---|
| Breadth-first drift | Many shallow questions across the idea | Depth follows dependency, not curiosity |
| Premature convergence | Collapsing a branch because the answer "felt obvious" | Obvious answers are the unexamined ones — ask anyway |
| Interrogation theatre | Long impressive list the user rubber-stamps | Volume is not rigor; fewer, sharper nodes |
| Passivity tax | "agreed, agreed, agreed" for 40 questions | Push back on contradictions; demand one disagreement |
| Grilling the ungrillable | Re-phrasing the same question for 3 waves | Stop. Route to a prototype. Return for a one-liner |
| Ledger drift | Wave 5 contradicts an answer from wave 2 | Recap the ledger at the top of every wave |
| Fact-as-question | Asking the user something you could have checked | Search first; only decisions go to the user |

---

## 12. Signals it's working

* The user disagreed with at least one recommendation.
* Later waves visibly build on earlier answers.
* A decision surfaced that the user had been making by accident.
* At the end, every settled point could be defended to an absent colleague.
* The risk table has no unexplained one-way-doors.

---

## Reference files

| File | Use for |
|---|---|
| `references/decision-tree-model.md` | Building, pruning, splitting nodes; root discovery |
| `references/profiles.md` | Per-profile checklists and intensity rules |
| `references/question-craft.md` | Seven archetypes, phrasing patterns, anti-leading |
| `references/adversarial-passes.md` | Red-team library, pre-mortem, assumption audit |
| `references/scoring.md` | Importance / reversibility / confidence rubric |
| `references/closure.md` | Closure checklist with verification steps |
| `references/ledger-and-handoff.md` | Ledger schema, conflict detection, handoff format |
| `references/worked-example.md` | Full 3-wave example, start to finish |

Load only the files the current profile needs. Do not preload all of them.
