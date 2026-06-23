# Subagent brief template

Subagents start cold — no memory of the conversation, the plan, or the codebase. Every brief must stand alone. Fill in every section; the cheaper the model, the more explicit each section must be.

```
## Goal
<One paragraph: what this task produces and why it exists in the larger project.
State the definition of done in one sentence.>

## Context (pre-packaged — do not re-explore)
- Stack & conventions: <language, framework, formatting/naming conventions observed in this repo>
- Relevant code, pasted so you don't have to find it:

  <file_path:line — paste the actual excerpt, signatures, types, or interfaces
   the agent needs. Include the patterns it should imitate.>

- How this connects to the rest: <what calls it / what it calls; the contract it must honor>

## Files you own
<Exact paths. You may create/edit ONLY these. If you believe another file
must change, STOP and report that instead of editing it.>

## Constraints
- <Hard requirements: API shape, performance, security, style, no new deps, etc.>
- Do not refactor or "improve" code outside the stated goal.
- Before creating or writing anything, check whether it already exists — a prior run may have started this. Resume or extend; don't blindly redo or overwrite.

## Verify before reporting done
Run: `<exact build/test/lint command>`
If it fails, fix and re-run. Do not report success with a failing check.

## (Research / data-collection tasks only) Journal as you go
Stream findings to `.supermax/<task-id>.result.md` as you gather them — not just in your final report. If your session ends early, whatever you've written there survives and the orchestrator resumes from it. Skip this if your work produces files directly — those are already your checkpoint.

## Deliverable
Report back: <e.g. "files changed with a one-line summary each, the verification
output, and any follow-ups or concerns — flagged, not silently handled.">
```

## Calibration by tier

- **Haiku** — leave zero judgment calls. Paste examples to copy, enumerate edge cases, spell out names. If you can't fully specify it, it isn't a Haiku task.
- **Sonnet** — specify the contract and conventions precisely; implementation details can be left to it.
- **Opus / Fable** — can take an open-ended problem statement, but still needs the pre-packaged context and the files-owned boundary.

## Anti-patterns

- "Look around the codebase and..." — never. You (the orchestrator) or the scout already looked; paste what was found.
- Briefs that describe the *whole project* — describe this task; one paragraph of surrounding context is the ceiling.
- Omitting the verification command — an agent that can't self-check ships its mistakes to your integration pass.
