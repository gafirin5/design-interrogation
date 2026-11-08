# Question Craft

A wave is only as good as its questions. Bad questions get answered with noise that looks like signal.

## The seven archetypes

Pick the archetype deliberately. Each one extracts a different kind of answer.

### 1. Boundary
*Where does this end?*
> "Does this service own the customer record, or read it from the CRM? If both write, who wins on conflict?"

Use to stop scope creep and implicit duplication.

### 2. Forcing function
*What makes this decision non-optional?*
> "If we shipped nothing, what breaks in 90 days? If nothing breaks, what is this for?"

Use when the idea's necessity is unexamined.

### 3. Failure
*How does this break?*
> "The nightly import fails at 02:00 on the day of a board report. What is the fallback?"

Use at depth 2 and in red-team waves.

### 4. Tradeoff
*Pick your poison.*
> "Consistency or availability during the cutover? Pick one to be wrong about for an hour."

Use when a decision has no free option. Naming that explicitly stops the user searching for one.

### 5. Reversibility
*How expensive is being wrong?*
> "If this is the wrong auth model in a year, what does the migration cost — one sprint, or a re-login for every user?"

Use to decide whether the decision needs depth-3 treatment at all.

### 6. Concrete-isation
*Replace an abstraction with an instance.*
> "Walk me through Tuesday morning: who opens what, and what do they see first?"

Use when answers are staying abstract. Specifics expose contradictions that abstractions hide.

### 7. Subtraction
*What if we removed this?*
> "What happens if we ship without the approval step? Who notices, and when?"

Use to prune. Most features are cheaper to remove at design time than after.

## Good vs bad phrasing

| Bad | Why | Good |
|---|---|---|
| "How should auth work?" | Unbounded | "Session cookies, JWT in localStorage, or server-side sessions with sticky routing?" |
| "What are the requirements?" | Asks the user to do your job | "Must a report survive a 3-day power outage? That decides storage." |
| "Do you think X is good?" | Invites agreement, not a decision | "X costs a week now and saves two later. Worth it?" |
| "Should we use Postgres or MySQL?" | Technology, not decision | "Do we need transactions across two writers? That's what picks the database." |
| "Any concerns about this?" | Vague, produces nothing | "What's the first thing that breaks on launch day?" |

## Anti-leading

A leading question hands over the answer and asks for a nod.

- **Don't** write a 3-paragraph argument then ask "agreed?"
- **Do** state the options neutrally, put the recommendation in the `→` line where it's visibly yours, and let the body stay balanced.

If you notice you've written more argument than question, cut the argument.

## One-word answers

Every question must be answerable with `ok` when the recommendation is accepted, and interpretable when rejected.

Test it: *"If the user says only 'yes', do I know exactly what got decided?"* If not, the question is compound or vague.

## Body length

- Quick profile: 1–2 sentences
- Standard: 1 short paragraph
- Deep/Exhaustive: up to 3 paragraphs **only** when the tradeoff genuinely needs it

Long bodies are a smell: they usually mean you're arguing instead of asking, or fusing two decisions.

## Tagging

Every question carries `importance` and `reversibility`:

```
**Q3 · N7 — Cutover window**  `importance 5 · one-way-door`
```

This is the user's queue-management tool. A wave where everything is `importance 5 · one-way-door` is a mitag — it means you failed to rank.

## Sequencing inside a wave

Order by: importance desc, then depth asc, then dependency (a question another question references goes first).

Group related questions adjacently so the user answers them in one mental breath.

## Questions to delete

- Anything answerable by looking at a file, a log, or the docs → go look
- Anything the implementer will decide anyway and can reverse freely → defer
- Anything whose answer cannot change the design → don't ask
- Anything you're asking only to look thorough → delete
