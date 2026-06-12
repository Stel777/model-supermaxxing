---
name: model-supermaxxing
description: Turns the currently-running model into an orchestrator that decomposes an ambitious project into tasks, triages each by difficulty, and dispatches them to subagents on the cheapest-capable model (Haiku/Sonnet/Opus/Fable) — keeping the hardest architecture and synthesis work for itself. Use for large, end-to-end builds or any ambitious project with a lot of work to parallelize across models. Invoke with /model-supermaxxing, optionally with args, e.g. /model-supermaxxing cost-aggressive full.
user-invocable: true
argument-hint: "[standard|cost-aggressive|quality-first] [checkpoint|full]"
metadata:
  version: "1.1.0"
  author: "Stel"
---

# Model Supermaxxing by Stel

**You — the model reading this right now — are the orchestrator.** Whatever model is active when this skill runs is the manager. Your job is not to do all the work yourself: break the project into tasks, route each task to the cheapest model that can do it well, run them in parallel where safe, and personally handle only the most ambitious work — architecture, hard problem-solving, integration, final synthesis.

The principle: **no overkill, in either direction.** Don't burn a frontier model on renaming a button. Don't send a subsystem redesign to Haiku. And don't spawn an agent at all for a task smaller than its own brief.

The mechanism is real: the `Task`/`Agent` tool accepts a `model` override of `haiku`, `sonnet`, `opus`, or `fable`, plus `isolation: "worktree"` and `run_in_background` for safe parallelism.

## The capability ladder

| Tier | Model | Built for |
|------|-------|-----------|
| 1 | **Haiku** | Trivial, mechanical, well-specified work: boilerplate, config edits, copy tweaks, simple CRUD, renames, formatting. |
| 2 | **Sonnet** | Moderate, self-contained work: a standard component, a normal feature, tests, a straightforward refactor, docs. |
| 3 | **Opus** | Complex work: tricky algorithms, cross-cutting logic, hard debugging, subsystem design, security-sensitive code. |
| 4 | **Fable / You** | The most ambitious work: overall architecture, key design decisions, integration, final review. The orchestrator keeps this tier. |

The deciding factor between tiers is **correctness risk, not task size**. When triage is unclear, read `references/routing-examples.md`.

## Arguments

The skill accepts up to two args: a **routing mode** (`standard` | `cost-aggressive` | `quality-first`) and a **deploy mode** (`checkpoint` | `full`). If both are given (e.g. `/model-supermaxxing cost-aggressive full`), skip the questions in step 3 entirely. If only one is given, ask only for the missing one.

## Workflow

### 1. Scout — don't explore with expensive tokens
If there's a codebase, spawn **one Explore agent on `haiku`** to map it: structure, stack, conventions, key files relevant to the request. You plan from its report. Only read files yourself when a decision genuinely hinges on details the scout can't be trusted with. (Skip the scout for greenfield or tiny projects.) If the request itself is ambiguous, ask sharp clarifying questions first — a bad task graph wastes the most money.

### 2. Decompose into a task graph
Break the work into concrete, self-contained tasks. For each: what it produces, **which files it owns**, what it depends on. Track with `TodoWrite`.

**Inline threshold:** any task whose brief would be longer than the task itself (one-line fixes, tiny renames, a config flag) — just do it yourself, inline. Agent spawning has overhead; below the threshold, dispatching *is* the overkill.

### 3. Confirm modes (unless given as args)
Use `AskUserQuestion`:
- **Routing mode** — `standard` (the ladder as written) · `cost-aggressive` (push work down; only genuinely hard → Opus; You/Fable for very little) · `quality-first` (floor is Sonnet; Opus for most real work).
- **Deploy mode** — `checkpoint` (present the plan, wait for approval, pause at each wave boundary) · `full` (autonomous A–Z; stop only for genuine blockers; report at the end).

### 4. Assign models and present the plan
Apply the routing mode to the graph. Present a table — `Task | Model | Files owned | Depends on` — so conflicts are visible at plan time. A task that *looks* small but has subtle correctness stakes belongs a tier up; big-but-mechanical belongs a tier down. If `checkpoint`, wait for approval; if `full`, post it and continue.

### 5. Dispatch — parallel where safe
- Independent tasks run **concurrently**: issue multiple `Task` calls in a single message, each with its `model` set. Cap concurrency at **~5**; bigger graphs run in batches.
- Dependent tasks run in **waves**: finish, verify, integrate, then launch the next wave.
- Two write-tasks must never share a file. If overlap is unavoidable, give those agents `isolation: "worktree"` and merge yourself, or serialize them.
- Use `run_in_background` for long-running agents so you keep working.
- **Briefs are everything.** Subagents start cold. Build each brief from `references/brief-template.md`: pre-package the context (paste relevant excerpts, signatures, conventions — don't make a cheap model re-explore), state exact files owned, constraints, the deliverable, and the **verification command the agent must run before reporting done**. A Haiku agent with the code in front of it ≈ a Sonnet agent that has to find it.

### 6. Verify each wave — escalation ladder
Check every agent's output as its wave completes (run the build/tests yourself if the agent's claim matters). On failure:
1. **Retry once at the same tier** with a sharper brief that names the specific failure.
2. Still failing → **escalate one tier up**, including the failed attempt and why it was wrong.
3. Still failing → **you do it yourself.**

Never build a later wave on top of unverified work. This ladder is what makes aggressive down-routing safe.

### 7. Integrate & final review
You do the integration — wiring parts together, resolving conflicts, coherence. Run the full build/test/lint. Then a final pass: yourself, or one fresh-eyes Opus/Fable reviewer agent on the assembled result. Fix what it finds.

### 8. Report
Summarize: what was built, the task→model breakdown (who did what, including escalations), verification results, follow-ups. Make the efficiency win visible.

## Rules of thumb
- Correctness risk, not size, decides the tier.
- Below the inline threshold, do it yourself; above it, never do Haiku-work personally.
- Front-load context into briefs, not back-and-forth.
- Cheaper models need explicit, spelled-out briefs; capable models can take open-ended problems.
- Never two agents writing the same file outside worktrees.
- You stay the integrator: agents produce parts, you make them a whole.
