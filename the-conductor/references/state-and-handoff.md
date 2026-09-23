# State & Handoff

The run's memory lives on disk, not in the conductor's context. The test for every state write: **a fresh session reading only `.conductor/` could resume the run without asking anything.**

## `.conductor/` layout (in the target project)

| File | Holds | Written |
|---|---|---|
| `charter.md` | goal, acceptance criteria, ceiling, routing, stop line | once at CHARTER (loop step 1) |
| `state.md` | the ledger: every unit's id, tier, substrate, status (PENDING / DISPATCHED / DONE / BLOCKED / QUARANTINED), gate result, artifact paths, token tally per wave | after **every** unit event |
| `decisions.md` | the decision log (decision-protocol.md format) | at each ruling, before acting |
| `PROGRESS.md` | ≤20-line human-readable snapshot: where the run is, what's in flight, tally vs ceiling | after every wave |
| `closeout.md` | close-out report | at STOP |
| `handoff.md` | next-session launch prompt | at STOP, if work remains |

## Compaction-safety rules

- Update `state.md` **before** dispatching the next wave — never batch state writes.
- State holds **pointers, not payloads**: artifact paths and one-line summaries, never file contents. The conductor reads big artifacts only via a worker's summary.
- After context compaction (or on any doubt), re-read `state.md` + `PROGRESS.md` and trust the disk over recollection. Worker-return vs state conflicts → reconcile from disk.
- Token tally: append each wave's spend to state.md; check against the ceiling before every dispatch. Worker spend is **reconciled from the Agent tool's reported usage figures** (each dispatch result carries them) — estimates are only for the conductor's own overhead, and each tally line marks which numbers are reported vs estimated.

## New-session-required — the only legitimate mid-run stops

1. Context quality degradation the conductor can detect in itself (losing track of ledger facts that are on disk, re-asking settled questions).
2. Harness limits (session termination, tool failures that survive retry).
3. A substrate change the session can't make (e.g., the run must move machines).

Everything else — long waits, many waves, big plans — is NOT a reason to stop; compaction plus disk state carries the run.

## Close-out report (`closeout.md`)

Top: plain-language TL;DR — did the run meet the acceptance criteria, yes or no. Then: scorecard (each criterion PASS/FAIL with evidence path — criteria are graded, never reworded); decision-log reference; budget spent vs ceiling; residuals (anything owed); **"for human review"** — every ruling a human should audit, and every stop-line item deferred.

## Handoff prompt (`handoff.md`) — the "HOW TO LAUNCH" pattern

```markdown
> **HOW TO LAUNCH:** open a fresh Claude Code session in <workspace root>, set the
> session model to the CONDUCTOR model in `.conductor/charter.md`, then paste everything below the line.

## State at handoff (<date>)
<3–6 bullets: ledger position, what's green, what's in flight, tally vs ceiling>
---
You are the conductor resuming <project> via the-conductor skill. Read
`.conductor/state.md`, `charter.md`, `decisions.md` first — the disk is the truth.
Resume at <exact next step>. Prior rulings hold. <Ceiling remaining: N.>
```

The handoff never duplicates state — it points at `.conductor/` and names the next step.
