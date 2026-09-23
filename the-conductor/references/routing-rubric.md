# Routing Rubric

Assign every unit a model tier at DECOMPOSE. Record the tier in state.md; overrides need a logged rationale. Tiers are capability levels, not model names — the charter's **Model map** binds each tier to a concrete model for the run.

| Tier | Gets | Never gets |
|---|---|---|
| **LIGHT** | Mechanical work with an unambiguous spec: renames, format conversions, template fills, boilerplate from an exact example, bulk summarization of many files into a fixed shape | Any judgment call — grading, weighing, or interpreting |
| **STANDARD** | Default execution: feature implementation, test writing, docs with substance, research with a defined question | — |
| **HEAVY** | Judgment-heavy work: design, review/grading, plan drafting, debugging that resists one obvious fix, anything the conductor would call ambiguous | — |
| **CONDUCTOR (self)** | Orchestration only: reading state lean, running gates/glue, deciding BLOCKED questions, writing .conductor/ files | Unit work of any kind — no code edits, no content authoring, no grading |

## The two rules that settle every argument

1. **Judgment → never LIGHT.** If grading, weighing, or interpreting is involved, LIGHT is out.
2. **Unsure → STANDARD.** When the tier is debatable, STANDARD. Escalate to HEAVY only when a unit's failure would be expensive (rework across other units, a wrong ruling baked into artifacts).

## Substrate

- **In-session subagent** (default): dispatched with the tier's model override; supports the BLOCKED → SendMessage resume loop.
- **Headless background session** (`claude --bg` / `claude -p` detached): only for units expected to exceed ~15 minutes wall-clock (large test suites, builds, overnight batches). These cannot do interactive Q&A — their contract must be fully self-contained, results land in files, and the conductor polls for completion. A unit likely to raise BLOCKED must not go headless.

Tool and command names in this skill are Claude Code's. On another harness, substitute its equivalents for three operations: dispatch a worker with a model override (Agent tool), resume a worker with its context intact (SendMessage), and run a worker detached (`claude -p`).

## Cost posture

The conductor is the expensive model; its context is the scarcest resource in the run. Every token the conductor spends reading artifacts a worker could summarize is waste. Route reading-heavy work down, judgment-heavy work up, and keep the conductor's own turns to glue, gates, and rulings.
