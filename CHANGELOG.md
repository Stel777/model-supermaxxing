# Changelog

## v1.2.0 — 2026-06-23

Durability pass: survive session limits without losing work — and do it leanly, since most of the protection costs near-zero tokens.

### Added
- **`.supermax/` run journal** — the orchestrator mirrors the task graph + wave status to `.supermax/plan.md` (the durable twin of `TodoWrite`, which dies with the session), so a dead *orchestrator* resumes the whole graph instead of losing it. Identified as the highest-value save — bigger than any single subagent dying.
- **Task-sizing rule** — decomposition now sizes each task to fit one agent session and splits anything that won't, preventing session-limit deaths at the source instead of only recovering from them.
- **Resumable-by-design briefs** — every brief tells the agent to check what already exists before building, so retries/resumes skip finished work instead of redoing or clobbering it. Near-zero cost; protects file-writing tasks without any journal.
- **Targeted result journaling** — research / synthesis / data-collection agents (whose output never hits disk) stream findings to `.supermax/<task-id>.result.md` as they go. File-writing and trivial agents skip it — their edits already are their checkpoint. Applied as an automatic routing rule, not a new user prompt.

### Changed
- Escalation ladder now distinguishes *wrong* work (restart cold) from *interrupted* work (resume from the journal), so a session-death retry continues instead of starting over.

## v1.1.0 — 2026-06-12

Reliability and efficiency overhaul: the difference between *describing* multi-model orchestration and being genuinely cheaper and safer than running everything on the big model.

### Added
- **Escalation ladder** — every wave is verified as it completes; failed tasks get one retry at the same tier with a sharper brief, then escalate one tier up, then the orchestrator does it itself. Makes aggressive down-routing safe.
- **Inline threshold** — tasks smaller than their own brief (one-liners, flag flips) are done by the orchestrator directly instead of paying agent-spawn overhead.
- **Scout phase** — a cheap Haiku Explore agent maps the codebase; the expensive orchestrator plans from the map instead of burning frontier tokens on grep.
- **Pre-packaged context briefs** — briefs must paste the relevant excerpts/signatures/conventions so cheap agents don't re-explore; every brief includes a self-verification command. Template in `references/brief-template.md`.
- **Worktree isolation & background dispatch** — `isolation: "worktree"` for overlapping write-tasks, `run_in_background` for long runners, concurrency capped at ~5.
- **Arguments** — `/model-supermaxxing [standard|cost-aggressive|quality-first] [checkpoint|full]` skips the runtime questionnaire.
- **File-ownership column** in the plan table — conflicts visible at plan time.
- `references/routing-examples.md` — triage calibration table, edge rules, and per-mode shifts.

### Changed
- SKILL.md restructured around the new workflow (scout → decompose → modes → assign → dispatch → verify/escalate → integrate → report); examples moved out to references for a leaner always-loaded core.

## v1.0.0 — 2026-06-11

Initial release: orchestrator decomposes an ambitious project, triages tasks by difficulty, and dispatches them to subagents on the cheapest capable model (Haiku/Sonnet/Opus/Fable), keeping architecture and synthesis for itself. Standard / cost-aggressive / quality-first routing modes; checkpoint vs. full A–Z deploy.
