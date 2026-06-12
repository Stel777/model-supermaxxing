# Model Supermaxxing

A Claude Code skill that turns **whatever model you're currently running** into an **orchestrator**. Instead of doing every part of an ambitious project itself, the orchestrator decomposes the work, triages each task by difficulty, and dispatches tasks to subagents on the **cheapest model that can do them well** — keeping only the hardest architecture and synthesis work for itself.

> The principle: **no overkill, in either direction.** Don't burn a frontier model on renaming a button. Don't send a subsystem redesign to a small model. And don't spawn an agent at all for a task smaller than its own brief.

## Why

Ambitious, end-to-end projects contain a mix of trivial, moderate, and genuinely hard work. Running all of it on a top-tier model is slow and expensive; running all of it on a cheap model is unreliable. This skill routes each task to the right tier, parallelizes the independent ones, and verifies every wave so mistakes from cheap models get caught and escalated — not built upon.

## The capability ladder

| Tier | Model | Built for |
|------|-------|-----------|
| 1 | **Haiku** | Trivial, mechanical, well-specified work — boilerplate, config, copy tweaks, simple CRUD, renames, formatting. |
| 2 | **Sonnet** | Moderate, self-contained work — a standard component, a normal feature, tests, a straightforward refactor, docs. |
| 3 | **Opus** | Complex work — tricky algorithms, cross-cutting logic, hard debugging, subsystem design, security-sensitive code. |
| 4 | **Fable / Orchestrator** | The most ambitious work — overall architecture, key design decisions, integration, and final synthesis/review. |

The deciding factor between tiers is **correctness risk, not task size**: a one-file race-condition fix goes to Opus; a 30-file mechanical rename goes to Haiku.

## How it works

1. **Scouts cheap** — a Haiku Explore agent maps the codebase; the expensive orchestrator plans from the map instead of exploring itself.
2. **Decomposes** the work into a task graph with dependencies and per-task file ownership. Tasks smaller than their own brief are done inline — no agent overhead.
3. **Asks you two things** (or takes them as arguments):
   - **Routing mode** — `standard` · `cost-aggressive` · `quality-first`.
   - **Deploy mode** — `checkpoint` (approve the plan, pause at each wave) · `full` (autonomous A–Z).
4. **Dispatches in parallel** — independent tasks batched concurrently (capped ~5), dependent tasks in waves, worktree isolation when write-tasks overlap, background execution for long runners.
5. **Briefs that work** — every subagent gets pre-packaged context (relevant code pasted in, not "go explore"), exact file ownership, and a self-verification command. Template in [references/brief-template.md](references/brief-template.md).
6. **Verifies every wave with an escalation ladder** — failure → one retry with a sharper brief → escalate one tier up → orchestrator does it itself. Later waves never build on unverified work.
7. **Integrates itself** — the orchestrator wires the parts together and owns the final review.
8. **Reports** the task→model breakdown, escalations included, so the efficiency win is visible.

The mechanism is real: each subagent runs on a per-agent `model` override (`haiku` / `sonnet` / `opus` / `fable`).

## Install

Clone into your Claude Code skills directory:

```bash
git clone https://github.com/Stel777/model-supermaxxing.git ~/.claude/skills/model-supermaxxing
```

Restart your Claude Code session, then invoke with:

```
/model-supermaxxing
```

## Usage

```
/model-supermaxxing                          # asks for routing + deploy mode
/model-supermaxxing cost-aggressive full     # skips the questions, runs A–Z
/model-supermaxxing quality-first checkpoint # bias up, approve each wave
```

Run it on any ambitious, multi-part project and let the orchestrator fan the work out across models.

## Repo layout

- [SKILL.md](SKILL.md) — the orchestrator playbook (always loaded when invoked)
- [references/brief-template.md](references/brief-template.md) — the subagent brief template, with per-tier calibration
- [references/routing-examples.md](references/routing-examples.md) — triage calibration table and edge rules
- [CHANGELOG.md](CHANGELOG.md)

---

**Author:** Stel · **Version:** 1.1.0
