# Profiles

The profile sets intensity. Pick it before wave 1 and state your inference so it can be overridden.

## Quick

**Target:** one concern, low stakes, or a throwaway idea.

- Waves: 1–2
- Questions per wave: 3–5
- Skip depths 3+ unless something explodes
- No adversarial pass, no scoring table
- End with a 5-bullet summary and one confirmation question

Checklist:
- [ ] Root decision named
- [ ] 3–5 shape decisions resolved
- [ ] One sentence: "the thing you're actually deciding is ___"
- [ ] Ask for confirmation

**Never use Quick for:** anything that touches production data, money, other people's time, or a public API.

## Standard (default)

**Target:** most features, plans, and business calls.

- Waves: 3–5
- Questions per wave: 5–9
- Depth 0 → 2 fully, depth 3 only where risk concentrates
- One light red-team wave at the end
- No formal risk table; inline confidence tags only

Checklist:
- [ ] Root + shape (depth 0–1) settled
- [ ] Mechanics (data, flow, failure) settled
- [ ] One red-team wave: "how does this fail?"
- [ ] Deferred list explicit
- [ ] Confirmation

## Deep

**Target:** architecture, migrations, anything expensive to undo.

Everything in Standard, plus:
- Waves: 5–8
- Questions per wave: 6–12
- Full depth-3 pass: concurrency, migration, rollback, exit
- Full red-team library (`adversarial-passes.md`)
- Pre-mortem required
- Risk register required (`scoring.md`)
- Every one-way-door gets explicit confidence rating

Checklist:
- [ ] All of Standard
- [ ] Pre-mortem run and its findings interrogated
- [ ] Every one-way-door named and rated
- [ ] Rollback story exists for every irreversible choice
- [ ] Risk table presented with confirmation

## Exhaustive

**Target:** one-way doors with org-wide or external blast radius. Regulatory, financial, or reputational exposure.

Everything in Deep, plus:
- No wave cap; run until the frontier is genuinely empty
- **Assumption audit** mandatory — enumerate every implicit premise
- **Contradiction sweep** mandatory
- **Second-order effects** wave mandatory
- Risk register ranked, with residual risk owner per row
- Ledger written to `interview-ledger.md` (the one case where this skill writes a file)
- Handoff block produced at the end

Checklist:
- [ ] All of Deep
- [ ] Assumption list produced, each item resolved or flagged
- [ ] Contradiction sweep clean
- [ ] Residual risk accepted explicitly by the user, per row
- [ ] Handoff block delivered

---

## Choosing

Infer from these signals, then state the inference:

| Signal | Suggests |
|---|---|
| "just sanity check", "quick thought" | Quick |
| "plan", "feature", "should we" | Standard |
| "migration", "architecture", "we're committing to" | Deep |
| "production data", "contract", "public API", "can't undo" | Deep or Exhaustive |
| Nervousness, hedging, "I'm worried about" | Go one level higher than you'd otherwise pick |

Escalation during a session is allowed and cheap: *"Two one-way-doors just surfaced — escalating to Deep."*

De-escalation is also allowed, but say why: *"These are all easily reversible; dropping to Standard and closing in two more waves."*

## Anti-pattern: profile inflation

Running Exhaustive on a naming decision wastes the user's attention and teaches them to rubber-stamp. Match rigor to reversibility, not to the user's enthusiasm.
