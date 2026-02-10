---
name: phase-discuss
description: Gather phase-specific decisions and write planning/phases/{phase}/CONTEXT.md. Use when clarifying a phase before planning or when the user asks to discuss a phase.
---

# Phase Discuss

Use this skill to clarify phase requirements and decisions, then write `CONTEXT.md` for the phase.

## Workflow

1. **Resolve phase**
   - If a phase number is provided, use it.
   - Otherwise, read `planning/STATE.md` and use `current_phase`.

2. **Validate inputs**
   - Require `planning/PLAN.md`.
   - If `planning/PLAN.md` is missing, direct the user to run `$plan-mode`.
   - If `planning/ROADMAP.md` is missing but `planning/PLAN.md` exists, bootstrap `planning/ROADMAP.md` from `planning/PLAN.md` using orchestrator parsing rules.
   - If `planning/STATE.md` is missing but `planning/ROADMAP.md` exists, bootstrap `planning/STATE.md` from `references/state-template.md`.
   - Never block only because `ROADMAP.md` or `STATE.md` is missing when `PLAN.md` exists.

3. **Prepare phase directory**
   - Read canonical `phase_dir` and `phase_name` from `planning/STATE.md` under `phases.<phase>`.
   - If `preferences.bootstrap.auto` is true in state, treat this canonical `phase_dir` as authoritative.
   - If the phase entry is missing in state, create it from `planning/ROADMAP.md`:
     - Set `phase_name`, `phase_dir`, `phase_status: pending`, and `step_status` keys.
   - If `phase_dir` is missing, compute it once from `<phase>-<phase_name-slug>` and persist it in `planning/STATE.md`.
   - Create `phase_dir` if missing.

4. **Clarify decisions**
   - Identify 3-4 gray areas based on the phase goal (UI, API, data, behavior, UX, edge cases).
   - Ask questions in popup-first batches using `request_user_input`:
     - Ask 1-3 questions per batch only.
     - Each question must have 2-3 mutually exclusive options.
     - Put the recommended option first.
   - Continue with additional batches until decisions are locked.
   - Never output a long questionnaire in plain text.
   - If `request_user_input` is unavailable, ask plain-text multiple-choice questions in chat.
   - Focus on requirements and behavior, not implementation.

5. **Write context**
   - Write `<phase_dir>/CONTEXT.md` using `references/context-template.md`.
   - Ensure frontmatter includes `step: discuss` and `status: complete`.

6. **Update state**
   - In `planning/STATE.md`:
     - Set `phases.<phase>.step_status.discuss: complete`
     - Set `phases.<phase>.step_status.plan: in_progress`
     - Set `phases.<phase>.phase_status: in_progress`
     - Set `current_step: plan`

7. **Next step**
   - Tell the user to run `$phase-plan <phase>`.

## Example Usage

Prompt: `Use $phase-discuss 01 to gather decisions for Phase 01.`

## Output Rules

- Keep questions tight and decision-oriented.
- Use popup selection questions when the tool is available.
- Always end with a **Next step** line.
