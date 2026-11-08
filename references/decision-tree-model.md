# Decision Tree Model

How to build, grow, prune, and split the tree.

## Root discovery

The root is **not** the idea as stated. The root is the decision that makes the rest of the tree exist or disappear.

| Stated idea | Bad root | Real root |
|---|---|---|
| "Build a dashboard" | "What charts?" | "Is a dashboard the answer, or is this a reporting problem with a cheaper fix?" |
| "Add SSO" | "Which IdP?" | "Who owns identity long-term — us or IT?" |
| "Rewrite the parser" | "Which language?" | "What is the parser costing us today that a rewrite would fix?" |

If you can't name the decision that would make the whole project unnecessary or obvious, that's N0 and it is the only question in wave 1.

## Branching heuristics

Expand a node into children when **one decision has sub-decisions that cannot be answered without it**. Common branch dimensions:

- **Actor** — who uses/owns/pays for this? (one child per distinct actor)
- **Lifecycle** — create, read, update, delete, migrate, deprecate (each is a decision)
- **Boundary** — where does this system end and another begin?
- **Failure** — what happens when each part is unavailable?
- **Time** — day 1 vs year 1 vs year 5
- **Cost** — build cost vs run cost vs exit cost

Do **not** branch on:
- Technologies (that's a consequence, not a decision dimension)
- UI details (until mechanics are settled)
- Anything whose parent is unresolved

## Depth budget

| Depth | What lives here | Typical node count |
|---|---|---|
| 0 | Framing: is this the right problem? | 1–2 |
| 1 | Shape: scope, actor, boundary | 3–6 |
| 2 | Mechanics: data, flow, failure | 5–12 |
| 3 | Edge cases: concurrency, migration, exit | 5–15 |

If depth 2 has fewer than 3 nodes, the tree is under-built. If depth 3 exceeds 15, the scope is too large for one session — split and interrogate separately.

## Node splitting: detecting a compound question

A node is compound if a single answer cannot be given. Test it by asking: *would two different people answer this with answers that don't conflict?*

> "Should the export be CSV or XLSX, and when should it run?"

Two decisions:
- N_a: format → CSV / XLSX / both
- N_b: trigger → manual / scheduled / on-change

A compound node **must** be split before it enters a wave. A user answering a compound question gives you an unparseable signal.

## Node merging: detecting false granularity

Two nodes should merge when neither can be decided independently even in principle.

> "Which folder do exports go in?" + "What name do export files get?"
> → merge into "Export destination and naming convention"

Merged nodes are fine as long as the merged question has one coherent answer.

## Pruning

Drop a node when **all** hold:
1. Importance ≤ 2 (see `scoring.md`)
2. Reversibility is easy
3. It can be decided by whoever implements it

Record pruned nodes in a **Deferred** list in the ledger so they're visible, not forgotten. A silently dropped node is an unstated assumption — the exact thing this skill exists to eliminate.

## Frontier computation (algorithm)

```
settled = {}
frontier = []

for node in tree:
    if node in settled: continue
    if all(parent in settled for parent in node.parents):
        frontier.append(node)

sort frontier by (importance desc, depth asc, id asc)
```

Run this after every wave. Never hand-wave the frontier — an unstated node in the frontier is a wave you will pay for later.

## Staleness

A settled node can become stale when a later answer contradicts it. When that happens:

1. Do not silently update it.
2. Raise it as the **first question of the next wave**, phrased as a conflict.
3. Re-open any node that inherited the contradiction.

Example:
> ⚠️ **Conflict** — N3 "Postgres" and N9 "deploy as a single static binary with no services" cannot both hold. Which wins?

## When the tree is wrong

If after two waves the questions feel repetitive or unimportant, the root is wrong. Stop, re-state the idea, and rebuild from a new root. Say so explicitly:

> "The tree isn't landing — these questions don't matter. I think the real question is X. Rebuild?"
