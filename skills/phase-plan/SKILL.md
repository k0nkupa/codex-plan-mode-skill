---
name: phase-plan
description: Create an executable phase plan from context and the overall project plan. Writes planning/phases/{phase}/PLAN.md. Use when a phase is ready to be planned.
---

# Phase Plan

Use this skill to convert phase context into an actionable plan.

## Workflow

1. **Resolve phase**
   - If a phase number is provided, use it.
   - Otherwise, read `planning/STATE.md` and use `current_phase`.

2. **Validate inputs**
   - Read canonical `phase_dir` from `planning/STATE.md` under `phases.<phase>`.
   - Require `<phase_dir>/CONTEXT.md`.
   - Require completion markers in context frontmatter: `step: discuss` and `status: complete`.
   - If missing, direct the user to run `$phase-discuss <phase>`.

3. **Plan**
   - Use `references/phase-plan-template.md` to write `<phase_dir>/PLAN.md`.
   - Pull goals and constraints from `CONTEXT.md` and the relevant phase section in `planning/PLAN.md`.
   - If `planning/RESEARCH.md` exists, apply recommendations from:
     - `Standard Stack`
     - `Architecture Patterns`
     - `Don't Hand-Roll`
     - `Common Pitfalls`
   - Ensure plan frontmatter includes `step: plan` and `status: complete`.
   - In chat output, include both:
     - `Planned in this phase` (summary of the plan contents: scope, ordered tasks, dependencies, tests, risks).
     - `Will be executed next` (the concrete execution preview, based on the first ordered tasks from `<phase_dir>/PLAN.md`).

4. **Update state**
   - In `planning/STATE.md`:
     - Set `phases.<phase>.step_status.plan: complete`
     - Set `phases.<phase>.step_status.execute: in_progress`
     - Set `phases.<phase>.phase_status: in_progress`
     - Set `current_step: execute`

5. **Next step**
   - Tell the user to run `$phase-execute <phase>`.

## Example Usage

Prompt: `Use $phase-plan 01 to create the plan for Phase 01.`

## Output Rules

- Ensure tasks are ordered and testable.
- Always show `Planned in this phase` as 3-7 concise bullets derived from the generated plan.
- Always show `Will be executed next` as 1-5 concrete bullets pulled from the plan's ordered tasks.
- Never output only status metadata; include plan substance and execution preview.
- Always end with a **Next step** line.
