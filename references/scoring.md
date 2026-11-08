# Scoring

Used by Deep and Exhaustive. Its purpose is **attention allocation**, not bureaucracy: it tells the user which questions deserve thought and tells you where the interrogation was too thin.

## Dimensions

### Importance (1–5)

| Score | Meaning | Test |
|---|---|---|
| 5 | Changes whether the project makes sense | If wrong, the whole thing is misdirected |
| 4 | Changes the shape of the system | If wrong, significant rework |
| 3 | Changes a component | If wrong, one part is rewritten |
| 2 | Changes a detail | If wrong, an afternoon |
| 1 | Cosmetic | If wrong, nobody notices |

### Reversibility

| Value | Meaning | Cost to undo |
|---|---|---|
| **easy** | Local change, no data or contract impact | Hours |
| **hard** | Touches stored data, a public interface, or another team | Weeks |
| **one-way-door** | External contract, published API, or irreversible data transform | Cannot undo cheaply, or at all |

A decision can be low-importance and still one-way-door (e.g. a URL path nobody cares about — but it's public). Score both dimensions independently.

### Confidence

| Value | Meaning |
|---|---|
| **knew** | The user stated it from knowledge |
| **guessed** | The user picked one, but hedged |
| **assumed** | Neither of you stated it; it was inferred |

`assumed` is the dangerous value. Any `assumed` + `one-way-door` row must be converted into an explicit question before closure.

### Blast radius

| Value | Scope of damage if wrong |
|---|---|
| local | One component, one team's code |
| team | One team's workflow |
| org | Multiple teams, shared processes |
| external | Customers, partners, public API, regulators |

### Residual risk

| Value | Meaning |
|---|---|
| none | Nothing left unresolved |
| mitigated | A guard exists (test, monitor, rollback) |
| accepted | User explicitly said "we'll live with it" |
| unknown | Nobody has assessed it — **blocks closure** |

---

## Priority formula

Sort the risk register descending:

```
priority = importance × reversibility_weight × confidence_weight × blast_weight

reversibility_weight: easy 1 · hard 2 · one-way-door 4
confidence_weight:    knew 1 · guessed 1.5 · assumed 2.5
blast_weight:         local 1 · team 1.5 · org 2 · external 3
```

Anything scoring ≥ 30 is a **hot spot**: return to it with a fresh node in a new wave, even if it was already "settled".

## Risk register format

```
📊 Risk register

| ID | Decision | Imp | Rev | Conf | Blast | Risk | Prio |
|---|---|---|---|---|---|---|---|
| N3  | Postgres over SQLite | 5 | one-way | knew | org | none | 60 |
| N7  | 5-min stale window | 4 | hard | guessed | external | mitigated | 36 |
| N11 | Single-region deploy | 4 | one-way | assumed | external | unknown | 90 ⚠️ |

⚠️ Hot spots: N11 — assumed, one-way-door, external blast radius, unassessed.
```

## Reading the register

- **Many `assumed` rows** → the interrogation skipped a branch. Run an assumption audit.
- **Many `unknown` rows** → you're about to close on unexamined risk. Keep going.
- **All rows `knew` + `none`** → either the design is genuinely simple, or the user is over-confident. Test one with a red-team question.
- **Uniform scores** → you're not ranking. Force a spread: the top row must be obviously more important than the bottom.

## What scoring is not

- Not a completeness metric — a short register with sharp scores beats a long one with 3s everywhere.
- Not a deliverable for Quick/Standard. Don't build tables nobody will read.
- Not a substitute for the closure checklist.
