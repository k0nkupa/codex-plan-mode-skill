---
name: phase-insert
description: Insert a new phase into planning/ROADMAP.md and planning/STATE.md without breaking numbering. Use when the user wants to add a phase mid-stream or append a new phase.
---

# Phase Insert

Use this skill to add a new phase and update the roadmap/state artifacts.

## Workflow

1. **Gather inputs**
   - Ask where to insert (after which phase number) and the new phase name + goal.
   - Ask whether this should be inserted between phases or appended to the end.

2. **Resolve phase number**
   - If appending: use next integer (zero-padded, e.g., 04).
   - If inserting between phases: use decimal numbering based on the previous phase (e.g., 02.1, 02.2).
   - Standardized numbering policy:
     - Base phases: `01`, `02`, `03`
     - Inserted phases: `02.1`, `02.2`
   - Never renumber existing phases.

3. **Update roadmap**
   - Insert a new line into `planning/ROADMAP.md`:
     - `- [ ] Phase {number}: {Name} - {Goal}`
   - Preserve ordering.

4. **Update state**
   - Add the new phase to `planning/STATE.md` using schema fields:
     - `phase_name`
     - `phase_dir`
     - `phase_status: pending`
     - `step_status` (`discuss`, `plan`, `execute`) all `pending`
   - Do not change `current_phase` unless the user asks to switch focus.

5. **Create directory**
   - Create canonical `phase_dir` from state.

6. **Next step**
   - Tell the user to run `$phase-discuss <phase>` if they want to start immediately.

## Example Usage

Prompt: `Use $phase-insert to add a phase after 02 named \"Payments\" with goal \"Integrate Stripe\".`

## Output Rules

- Always show the resolved phase number.
- Always include a **Next step** line.
