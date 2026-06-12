# Routing examples — triage judgment

The tier is decided by **correctness risk, not size**. Cheap models are great at volume, weak at subtlety. Use these calibration points when a task doesn't obviously fit a tier.

## Worked examples

| Task | Tier | Why |
|------|------|-----|
| "Add a CRUD endpoint matching the 6 existing ones" | **Haiku** | The pattern is established; the work is imitation. |
| "Rename `userId` to `accountId` across the repo" | **Haiku** | Spans many files but is mechanical and verifiable. |
| "Update all copy on the marketing pages from the new doc" | **Haiku** | Volume, zero judgment. |
| "Build the settings page component" | **Sonnet** | Standard, self-contained feature work. |
| "Write tests for the orders service" | **Sonnet** | Needs comprehension, follows established patterns. |
| "Refactor this 400-line file into modules" | **Sonnet** | Sizeable but low-subtlety; conventions are pasted in the brief. |
| "Design the caching layer and its invalidation strategy" | **Opus** | Design with non-obvious failure modes. |
| "Fix this intermittent race condition" | **Opus** | One file, but genuinely hard — the canonical risk-over-size case. |
| "Implement the auth/permission checks" | **Opus** | Security-sensitive; a subtle mistake is expensive. |
| "Decide the overall architecture, then integrate all subsystems" | **You / Fable** | Orchestrator-tier: global coherence is the whole job. |

## Edge rules

- **Looks small, stakes are subtle → one tier up.** Anything touching auth, payments, concurrency, data migrations, or public API contracts never goes to Haiku, regardless of line count.
- **Looks big, fully specified and repetitive → one tier down.** If the brief can enumerate every decision, the size is just volume.
- **Read-only research/scouting → Haiku Explore agent** almost always. Wrong answers are cheap to detect and the work is mostly retrieval.
- **Below the inline threshold → no agent at all.** If writing the brief takes longer than the task (one-liners, flag flips), the orchestrator does it inline.
- **Escalating up (Opus → Fable as a subagent)** is allowed even when the orchestrator is Opus — reserve it for an isolated, genuinely frontier-hard subproblem, not general "this seems important."

## Routing-mode shifts

- **standard** — the table above as written.
- **cost-aggressive** — each row's *first plausible* tier wins; Opus only for the genuinely hard rows; You/Fable kept for integration and final review only.
- **quality-first** — Haiku rows move to Sonnet; Sonnet rows with any design content move to Opus; the table's Opus/Fable rows are unchanged.
