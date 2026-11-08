# Ledger and Handoff

## Why a ledger

Long interrogations drift. Wave 5 quietly contradicts wave 2, and nobody notices until build time. The ledger is the defence: a compressed, explicit record of what has been decided, replayed at the top of every wave.

Default: **in-conversation only.** Write `interview-ledger.md` only for the Exhaustive profile, or when the user asks.

## Schema

```yaml
profile: deep
root: "Weekly CSV reconciliation for the accounts team"
wave: 4
nodes:
  N1: {q: "Is reconciliation the right problem?", a: "yes", status: settled}
  N2: {q: "Who uses it?", a: "one accountant", status: settled}
  N3: {q: "Storage?", a: "Postgres", status: settled, rev: one-way-door, conf: knew}
  N5: {q: "Conflict rule?", a: null, status: blocked, parent: N3}
  N9: {q: "Export format?", a: "XLSX", status: settled}
deferred:
  - {id: N14, q: "Filename convention", why: "implementer's call, easy to reverse"}
conflicts:
  - {a: N3, b: N11, note: "Postgres vs no-services deployment"}
risks: []
```

## Compact display form

For the top of each wave:

```
📋 Settled (4) · N1 Idea: weekly CSV reconcile. N2 User: one accountant.
N3 Storage: Postgres (one-way-door). N9 Export: XLSX.
⏸ Blocked (2): N5 (on N3), N10 (on N4).
🗑 Deferred (1): N14 filename convention — implementer's call.
⚠️ Conflict: none.
```

Keep it under ~6 lines. If it doesn't fit, you're narrating instead of indexing.

## Conflict detection rules

A conflict exists when two settled nodes cannot both be true. Check these classes after every wave:

| Class | Example |
|---|---|
| State vs deployment | Managed DB vs single static binary |
| Latency vs consistency | "instant" vs "strongly consistent across regions" |
| Team vs surface | "one person maintains it" vs "5 services + queue + cache" |
| Budget vs redundancy | "cheap" vs "multi-region failover" |
| Deadline vs migration | "live in 3 weeks" vs "backfill 2 TB with verification" |
| Security vs convenience | "no login" vs "audit trail per user" |

When found:
1. Name both node IDs.
2. State the incompatibility in one sentence.
3. Ask which yields, as the **first question of the next wave**.
4. Re-open any node that inherited the losing side.

Never resolve a conflict yourself by picking the one you like.

## Handoff

After confirmation, produce a block the user can paste into a spec, a ticket, or a build session. This is the artefact that makes the interrogation worth the time.

````markdown
## Design decisions — <idea name>
Profile: <profile> · <n> waves · <m> decisions · <date>

### The idea
<one paragraph>

### Decisions
1. **<decision>** — <what was chosen> · why: <reason> · reversibility: <value>
2. ...

### Explicitly out of scope
- ...

### Deferred to implementation
- ...

### Accepted risks
- **<risk>** — accepted by <who>, because <why>, reviewed <when>

### Assumptions to verify
- <assumption> — verify by <how>, owner <who>

### Open questions requiring a prototype
- <question> — cannot be answered by discussion
````

Deliver it as a fenced block so it copies cleanly. Do not write it into the repo unless asked.

## Feeding the next step

If the user wants to move into building, the handoff block is the input. Do not re-interrogate from scratch — carry the ledger forward. If a downstream step needs a decision the ledger marks `deferred`, that's a signal the deferral was wrong; reopen it.

## Lifecycle

| Event | Action |
|---|---|
| New wave | Append; never rewrite history |
| Answer contradicts settled node | Add to `conflicts`, do not overwrite |
| Node pruned | Move to `deferred` with a reason |
| Session resumed | Re-read the ledger recap before asking anything |
| Session abandoned | Ledger is still the artifact — deliver it |
