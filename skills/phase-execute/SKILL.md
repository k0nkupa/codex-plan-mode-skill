---
name: phase-execute
description: Execute a phase plan and write planning/phases/{phase}/SUMMARY.md with status updates. Use when implementing a phase plan.
---

# Phase Execute

Use this skill to implement the phase plan and summarize results.

## Workflow

1. **Resolve phase**
   - If a phase number is provided, use it.
   - Otherwise, read `planning/STATE.md` and use `current_phase`.

2. **Validate inputs**
   - Read canonical `phase_dir` from `planning/STATE.md` under `phases.<phase>`.
   - Require `<phase_dir>/PLAN.md`.
   - Require completion markers in plan frontmatter: `step: plan` and `status: complete`.
   - If missing, direct the user to run `$phase-plan <phase>`.

3. **Execute**
   - Implement the tasks in the phase plan.
   - Run relevant tests by default (ask if the scope is large).
   - Update docs when behavior changes.

4. **Write summary**
   - Write `<phase_dir>/SUMMARY.md` using `references/summary-template.md`.
   - Ensure summary frontmatter includes `step: execute` and `status: complete`.
   - In chat output, include both:
     - `Executed in this phase` (what was implemented, files changed, tests run, outcomes).
     - `What remains` (known gaps, deferred items, and next-phase carryover if any).

5. **Update state and roadmap**
   - In `planning/STATE.md`:
     - Set `phases.<phase>.step_status.execute: complete`
     - Set `phases.<phase>.phase_status: complete`
   - Set `current_phase` to the next phase (if any) and `current_step: discuss`.
   - Update `planning/ROADMAP.md` to check off the completed phase.

6. **Next step**
   - If another phase exists, tell the user to run `$phase-discuss <next_phase>`.
   - Otherwise, announce completion and suggest a wrap-up summary.
   - Always mention optional insertion: `$phase-insert`.

## Example Usage

Prompt: `Use $phase-execute 01 to implement Phase 01 and summarize results.`

## Output Rules

- Always include a **Next step** line.
- Always show `Executed in this phase` as 3-7 concise bullets.
- Always show `What remains` as 1-5 concrete bullets.
- Never output only status metadata; include execution substance and remaining work.
- Summaries should list tests run and known gaps.
