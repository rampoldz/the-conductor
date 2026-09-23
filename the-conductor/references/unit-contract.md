# Unit Contract

Every worker dispatch uses this contract. A unit is the smallest independently verifiable piece of work — sized to fit one worker's fresh context (roughly: one component, one document, one review; if the objective needs "and", consider splitting).

## Dispatch prompt — required elements, in order

1. **Objective** — one paragraph ending with the done-check ("Done when: …").
2. **Read first** — the exact file paths (and section refs) the worker needs. Nothing else; workers never explore beyond this list plus their own output paths.
3. **Files-may-touch** — exact list. Touching anything outside it fails the unit.
4. **Verification you must leave green** — the command(s) the conductor will re-run after you return. State them verbatim.
5. **Question policy** — "If you hit a decision your refs don't cover, STOP BEFORE mutating any file and return BLOCKED with the question and your recommended answer. Do not guess on anything that would be expensive to reverse."
6. **Return format** — final message must be exactly:

```
STATUS: DONE | BLOCKED | FAILED
SUMMARY: <≤15 lines — what changed, what you verified, anything surprising>
ARTIFACTS: <paths created/modified>
QUESTION: <BLOCKED only — one decision, the options, your recommendation>
```

7. **Standing rules** — workers never commit, never install anything global, never spawn their own sub-agents, never engage the user.

## Dispatch parameters

- `model`: per references/routing-rubric.md.
- Substrate: in-session subagent by default; headless session only per the rubric's long-running rule.
- Independent units go out in the same wave (parallel). A unit that consumes another's artifact waits.

## Handling returns

- **DONE** → conductor runs the verification itself (GATE), **plus a fence check**: confirm nothing outside the unit's files-may-touch list changed (inspect the workspace — mtimes, a quick diff of fenced files). Worker say-so is never sufficient for either.
- **BLOCKED** → decide per references/decision-protocol.md, then resume the SAME worker via SendMessage with the ruling (its context is intact). Never re-dispatch fresh for a question.
- **FAILED / gate RED** → one re-dispatch to a fresh worker with the failure evidence attached. Still red → quarantine: mark the unit BLOCKED-QUARANTINED in state.md, route around it if the plan allows, else circuit-break (decision-protocol.md).
- Malformed return (no STATUS line) → treat as FAILED; the one re-dispatch includes the contract verbatim.

## Re-dispatch budget

One per unit. The budget resets only if the conductor materially changes the unit (new refs, widened files-may-touch) — and that change is a logged decision.
