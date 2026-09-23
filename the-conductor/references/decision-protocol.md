# Decision Protocol

The conductor holds full delegated authority: it resolves every open question itself and never waits for a human. The price of that authority is a complete, auditable decision log.

## Decide → log → resume

On a BLOCKED return (or any fork the plan doesn't settle):

1. **Ground the decision** — check, in order: the charter (goal, acceptance criteria, guards), the plan, evidence already on disk. Most questions die here.
2. **If still open, rule on the merits** — prefer the option that (a) keeps acceptance criteria testable, (b) is cheapest to reverse, (c) fails toward scrutiny rather than toward silence. The worker's recommendation is input, not the ruling.
3. **Log it** in `.conductor/decisions.md` — one line, before acting on it:

```
- [D-###] <date> <unit-id> — Q: <the question> — RULING: <what was decided> — WHY: <one line> — evidence: <path/§ or "charter">
```

4. **Resume the worker** via SendMessage with the ruling stated plainly (what to do, not the deliberation). Same worker, context intact — never a fresh dispatch for a question.

Rulings are durable: later units get the same answer to the same question (check the log before ruling). Reversing a ruling is itself a logged decision citing new evidence.

## What is never decidable (stop line)

Honest reporting, irreversible/destructive actions outside the workspace, the token ceiling, and clean session handoff — per the charter's stop line. A question that lands on the stop line is not answered; it goes in the close-out's "for human review" list and, if it blocks the plan, triggers a circuit-break.

## Circuit breakers — STOP and close out, don't flail

- A unit fails its gate after its one re-dispatch and cannot be routed around.
- Any stop-line item is reached (ceiling, destructive fork, honesty conflict).
- The same question keeps returning after being ruled twice — the plan is wrong; stop and say so.
- The conductor catches itself doing unit work (editing code, authoring content, grading) instead of delegating.
- Worker returns are contradicting the state file — reality has diverged from the ledger; reconcile from disk, and if that fails, stop.

A circuit-break is not failure-by-panic: it produces the same close-out + handoff as a clean stop, with the breaker named in the TL;DR.
