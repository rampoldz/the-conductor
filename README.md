# The Conductor

A [Claude Code](https://claude.com/claude-code) skill that runs an entire project to completion unattended. The conductor decomposes a goal or `plan.md` into verifiable units, delegates every unit to tiered worker agents, rules on every question with full delegated authority, and stops only for a clean handoff.

The run's memory lives on disk in `.conductor/`, never in the conductor's context. A fresh session reading only that directory can take over mid-run.

## Install

Copy the `the-conductor/` folder into your skills directory:

```
cp -r the-conductor ~/.claude/skills/
```

## Use

The skill never auto-triggers. Invoke it explicitly:

```
/the-conductor <goal or path/to/plan.md>
```

The launch prompt may add a token ceiling and extra guards. For single tasks or attended work, don't use it.

## How it works

1. **CHARTER**: fill the charter template into `.conductor/charter.md`. Nothing dispatches before it exists.
2. **PLAN**: no plan? Dispatch a HEAVY-tier planner and gate the result.
3. **DECOMPOSE**: define units and assign each a tier.
4. **DISPATCH**: independent units go out in parallel waves.
5. **RULE**: on BLOCKED, decide, log the ruling, then resume the same worker.
6. **GATE**: the conductor re-runs every unit's verification itself.
7. **RECORD**: update state after every unit event.
8. **STOP**: write `closeout.md`, and `handoff.md` if work remains.

## Model tiers

The skill is model-agnostic. Work is routed to capability tiers, and the charter's **Model map** binds each tier to a concrete model for the run:

| Tier | Gets |
|---|---|
| LIGHT | Mechanical work with an unambiguous spec |
| STANDARD | Default execution: features, tests, docs, defined research |
| HEAVY | Judgment-heavy work: design, review, planning, hard debugging |
| CONDUCTOR | Orchestration only, never unit work |

Two rules settle every argument: judgment never goes to LIGHT, and when unsure, use STANDARD.

## Files

```
the-conductor/
  SKILL.md                         the loop, hard rules, red flags
  references/
    charter-template.md            per-run constitution, including the Model map
    unit-contract.md               dispatch prompt contract and return handling
    routing-rubric.md              tier assignment and substrate rules
    decision-protocol.md           decide, log, resume; circuit breakers
    state-and-handoff.md           .conductor/ layout, close-out, handoff prompt
```

## Other harnesses

Tool and command names are Claude Code's. On another harness, substitute equivalents for three operations: dispatch a worker with a model override, resume a worker with its context intact, and run a worker detached. See the Substrate section of `routing-rubric.md`.

## License

MIT
