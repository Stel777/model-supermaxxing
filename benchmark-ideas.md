# Benchmark Ideas

## Efficiency estimate (theoretical)

Token cost comparison for a typical ambitious project (10 tasks: 4 trivial, 4 moderate, 2 hard):

| Source of savings | Rough impact |
|---|---|
| Routing mechanical work to Haiku (vs Opus/Fable) | ~20x cheaper per token on those tasks |
| Scout phase (Haiku explores, not orchestrator) | Saves ~500–2000 orchestrator tokens on codebase mapping |
| Inline threshold (skip agent spawn for tiny tasks) | Eliminates ~200–500 tokens of overhead per trivial task |
| Parallel execution | Not token savings, but 2–4x faster wall time on independent task batches |

### Projected cost reduction vs. running everything on one top-tier model

- **Standard mode**: ~40–60% cost reduction
- **Cost-aggressive mode**: ~65–75% cost reduction

Wide uncertainty — depends entirely on the task mix of the actual project.

---

## Benchmark design

### Goal
Compare "naive" (everything on one model) vs. model-supermaxxing on the same fixed project, measuring:
1. Approximate token counts per step
2. Wall time
3. Output quality (does the result actually work?)

### Proposed test project
**"Build a simple CLI task manager in Python"**
- Features: add / list / complete / delete tasks, persisted to JSON
- Small enough to finish in one session
- Big enough to have a natural mix of trivial, moderate, and complex work

### Protocol
1. Run naive baseline (all tasks on a single chosen model), log token usage per step
2. Run `/model-supermaxxing` on the same spec, log token usage and model assignments
3. Compare cost, time, and quality side-by-side

### Why this project works as a benchmark
- Has clearly separable subtasks (data layer, CLI parsing, formatting, tests)
- Includes mechanical work (Haiku-tier) and design decisions (Opus-tier)
- Output is verifiable: run the CLI, check all four operations work
