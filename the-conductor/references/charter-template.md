# Charter Template

At CHARTER (loop step 1), copy this template to `<project>/.conductor/charter.md` and fill every field. The charter is the conductor's constitution for the run: every later decision cites it. If a field cannot be filled from the goal/plan, the conductor fills it with a default and logs that as decision D-001.

```markdown
# Conductor Charter — <project name>

**Run started:** <date> · **Conductor:** <model, from Model map> · **Entry:** goal | plan.md
**Workspace root:** <absolute path — the only tree workers may touch>

## Goal
<1–3 sentences. What done looks like, specific enough to grade.>

## Acceptance criteria
<Copied from plan.md, or derived at CHARTER. Each one testable. These are what the
close-out scorecard grades against — they may be marked FAILED, never reworded to pass.>
- [ ] <criterion>

## Token ceiling
<N tokens, or "none set">. Tally in state.md after every wave. Ceiling hit = hard stop
(close-out + handoff), never silence.

## Routing table
Defaults from references/routing-rubric.md. Per-unit overrides allowed with a logged rationale.
**Model map:** LIGHT=<model> · STANDARD=<model> · HEAVY=<model> · CONDUCTOR=<this session's model>
Filled from the launch prompt or the harness's available models; if guessed, logged as a decision.

## Delegation grant
Full authority: the conductor resolves every open question itself and never waits for a
human. Every call is logged in decisions.md with a one-line rationale for later audit.

## Stop line (non-delegable — overrides the delegation grant)
1. Honest reporting — never fabricate or paper over a failed gate, test, or grade.
2. No irreversible/destructive actions outside the workspace root: no force-push, no
   deleting pre-existing data, no publishing to external services, no real user or customer data.
3. Token ceiling (above) is a hard stop.
4. New-session-required (context degradation, harness limit) → stop cleanly with a
   launch-ready handoff, never degrade quietly.

## Run-specific guards
<Anything from the launch prompt: files never to touch, extra constraints, deadlines.
"None" is a valid entry.>
```
