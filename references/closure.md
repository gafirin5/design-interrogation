# Closure

Closure is a gate, not a feeling. Run every check; report the result honestly.

## The checklist

### 1. Tree exhaustion

```
for node in tree:
    assert all(parent in settled for parent in node.parents)
```

- [ ] No node has an open parent
- [ ] No node is in an undefined state (not settled, not deferred, not blocked-forever)
- [ ] Every deferred node appears in the Deferred list with a reason

### 2. Wave hygiene

- [ ] No wave introduced an option that was never decided
- [ ] No wave contained a question whose parent was still open
- [ ] No wave was sent while a previous wave was unanswered

### 3. Uncertainty handling

- [ ] Every "I don't know" resolved to one of: **prototype**, **dated deferral**, **accepted risk**
- [ ] No "we'll figure it out later" without a named owner
- [ ] Every ungrillable question identified and routed to something real to look at

A "I don't know" left as-is is not closure. It's a deferred bug in the design.

### 4. Consistency

- [ ] No two settled decisions contradict
- [ ] No decision from a later wave silently overrode an earlier one without being flagged
- [ ] Ledger recap in the final wave matches the actual settled set

### 5. Risk

- [ ] Every one-way-door has an explicit confidence rating
- [ ] No row has residual risk `unknown`
- [ ] Every `accepted` risk was accepted by the user in words, not inferred
- [ ] No hot spot (prio ≥ 30) left unvisited

### 6. Understanding

- [ ] You can restate the idea in one paragraph without referring to the transcript
- [ ] The user confirmed explicitly — not "sure", not silence, but an actual affirmation

---

## Reporting closure

Do not announce completion. Show the evidence and ask:

```
✅ Tree exhausted (14 nodes, 5 waves)

Settled: <compact list>
Deferred: <list with reasons>
Accepted risks: <list>
Open one-way-doors: <none | list>

I understand this as: "<one paragraph restatement>"

Does that match what you have in your head?
```

## What fails closure

| Situation | Verdict |
|---|---|
| Frontier empty, but one assumption unexamined | Not closed — run audit |
| Everything settled, no contradictions checked | Not closed — run sweep |
| User said "ok" to the summary but never answered wave 5 | Not closed — resend wave 5 |
| Risks listed but never accepted | Not closed — ask for explicit acceptance |
| You feel done | Not a criterion |

## Escape hatch

If closure keeps failing after three attempts on the same branch, the branch is ungrillable. Say so:

> "We've circled this three times. It's not a decision we can make in conversation — it needs a prototype. Proposing we close the rest and mark this as: *prototype required before build*."

That is a legitimate closure state, and better than pretending.

## Anti-closure

Never close because:
- The conversation is long
- The user seems bored
- You have enough for a plan (enough for a plan ≠ settled)
- The remaining questions feel small (small questions are how designs rot)

Closing early is the single most expensive failure in this skill, because the cost lands months later on someone else.
