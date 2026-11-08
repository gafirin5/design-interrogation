# Adversarial Passes

Once the frontier narrows, stop extending the tree and start attacking it. Extension produces coverage; attack produces truth.

Run passes in this order. Each pass is **one wave** formatted like any other.

## Pass 1 — Red team

Convert the settled design into an attack surface. For each settled decision, ask "how is this wrong?"

### Attack library

Use these, not generic objections.

**Data**
- What is the schema *after* the first real-world input that doesn't fit?
- What happens to records written by version N-1 after the upgrade?
- Where is the same fact stored twice, and who wins?
- What deletes this, and what is left behind after the delete?

**Load / time**
- 10× the current volume — which query dies first?
- What happens at the exact second of the cutover if a write lands?
- Which operation is O(n) against total history and therefore grows forever?

**Dependency**
- This depends on service X. X is down for 6 hours. What does the user see?
- X changes its API with 30 days notice. How many files change?
- Who else depends on us in a way we haven't declared?

**Human**
- Which decision requires a person to remember something?
- What does the error message say, and can a non-author act on it?
- Who is on call for this at 3am, and do they have permission to fix it?

**Security / trust**
- What is the blast radius of one leaked credential?
- Which input is trusted that shouldn't be?
- What can an insider do that they shouldn't be able to?

**Economic**
- What does this cost per month at 10× and at 0.1× usage?
- What's the exit cost if we abandon it in a year?

Pick 5–9 attacks that this specific design is actually exposed to. Do not walk the whole library — that's theatre.

## Pass 2 — Pre-mortem

One question, then its children:

> **Q · Pre-mortem** — It's eight months from now and this has failed badly enough that it was reverted or abandoned. What killed it?

Then ask for 3–5 candidate causes without judging them. Each cause becomes a node in the next wave: *is this cause plausible, and what would prevent it?*

Rules:
- Do **not** offer your own causes first — you'll anchor the user.
- After they answer, you may add causes they missed, labelled as yours.

## Pass 3 — Assumption audit (Exhaustive; Deep optional)

Enumerate every implicit premise in the settled design. Present as a table, not questions:

```
🧾 Assumptions
| # | Assumption | Held because | If false |
|---|---|---|---|
| A1 | Volume stays under 10k/day | current usage | index strategy changes |
| A2 | One team owns both services | org chart | need a contract + versioning |
| A3 | Users tolerate a 5-min stale window | nobody asked | need push, not poll |
```

Then ask: *"Which of these are wrong, or unverified?"* Every unverified high-impact assumption becomes a node.

An assumption the user has never questioned is the highest-value thing this skill can find.

## Pass 4 — Contradiction sweep

Check every pair of settled decisions for mutual exclusivity. Present only real conflicts:

> ⚠️ **N3 vs N9** — "Postgres" and "single static binary, no services" contradict. Which holds?

Common contradiction classes:
- **Deployment** vs **statefulness**
- **Latency target** vs **consistency model**
- **Team size** vs **operational surface**
- **Budget** vs **redundancy**
- **Time-to-market** vs **migration safety**

## Pass 5 — Second order (Exhaustive)

Not "what breaks" but "what changes that we didn't intend":

> Once this exists, what becomes easier that shouldn't be? What becomes unnecessary? Who starts depending on it that we don't want depending on it?

This is where you find the feature that becomes load-bearing for a team it was never designed for.

---

## Integrating findings

Attack results are **not** automatically new requirements. Each finding becomes a question:

| Finding | Question form |
|---|---|
| Plausible failure | "Accept the risk, mitigate now, or add a guard?" |
| Unverified assumption | "Verify it now (I'll dispatch exploration), or accept it as risk?" |
| Contradiction | "Which of the two yields?" |
| Second-order effect | "Does this change the design, or is it out of scope?" |

## When to skip

- **Quick** profile: skip all passes.
- **Standard**: pass 1 only, 3–5 attacks.
- **Deep**: passes 1, 2, 4.
- **Exhaustive**: all five.

Skipping a pass is fine. Silently skipping it, then claiming the design is "fully examined", is not.
