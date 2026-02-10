---
name: phase-orchestrator
description: Coordinate a phased workflow across plan-mode, phase-discuss, phase-plan, and phase-execute using planning/ artifacts (PLAN.md, ROADMAP.md, STATE.md, phase folders). Use when the user asks to orchestrate or navigate multi-phase work, wants a phase-by-phase workflow, or asks "what's next" for phases.
---

# Phase Orchestrator

Use this skill to guide the next phase action and keep `planning/` artifacts consistent. This flow pauses after each phase step and tells the user what to do next.

## Workflow

1. **Prerequisites**
   - If `planning/PLAN.md` is missing, instruct the user to run `$plan-mode` and stop.

2. **Ensure roadmap**
   - If `planning/ROADMAP.md` is missing, derive phases from `planning/PLAN.md` and write it using `references/roadmap-template.md`.
   - Use this numbering policy everywhere:
     - Base phases: zero-padded integers (`01`, `02`, `03`)
     - Inserted phases: decimal suffix (`02.1`, `02.2`)
   - Parse with explicit rules:
     - Phase heading regex: `^### Phase ([0-9]{2}(\\.[0-9]+)?): (.+)$`
     - Goal regex: first bullet after heading that matches `^- Goal: (.+)$`
   - If a phase name or goal is missing, fill with placeholders (`TBD Phase 01` / `Goal TBD`) without asking.

3. **Ensure state**
   - If `planning/STATE.md` is missing, create it from `references/state-template.md` as strict YAML.
   - Persist canonical fields for each phase:
     - `phase_name`
     - `phase_dir`
     - `phase_status`
     - `step_status` (`discuss`, `plan`, `execute`)
   - Create `phase_dir` once here and treat it as source of truth. Downstream skills must never recompute slugs.
   - Respect and preserve preferences if present:
     - `preferences.bootstrap.auto`
     - `preferences.research.enabled`
     - `preferences.research.mode`
   - If preferences are missing, initialize defaults from `references/state-template.md`.
   - Default `current_phase` to the first phase and `current_step` to `discuss`.

4. **Determine next action**
   - Read `planning/STATE.md` as YAML.
   - If `preferences.bootstrap.auto` is true, treat plan-mode bootstrap artifacts (`ROADMAP.md`, `STATE.md`) as authoritative and avoid recomputing phase metadata unless invalid.
   - Auto-advance using completion markers (not file existence):
     - `<phase_dir>/CONTEXT.md` counts as complete only if frontmatter has `step: discuss` and `status: complete`.
     - `<phase_dir>/PLAN.md` counts as complete only if frontmatter has `step: plan` and `status: complete`.
     - `<phase_dir>/SUMMARY.md` counts as complete only if frontmatter has `step: execute` and `status: complete`.
   - When marker checks pass, update `step_status` and `phase_status` in `STATE.md`.
   - If a step is complete, advance `current_step` to the next step for that phase.
   - Route by `current_step`:
     - `discuss` -> tell user to run `$phase-discuss <phase>`
     - `plan` -> tell user to run `$phase-plan <phase>`
     - `execute` -> tell user to run `$phase-execute <phase>`
   - In chat output, always include:
     - `Current state` (phase, current_step, step_status, phase_status, and key artifact marker status).
     - `Next action reason` (why this route was selected, based on state and marker checks).

5. **After execution**
   - If more phases remain, direct to `$phase-discuss <next_phase>`.
   - If no phases remain, declare completion and offer a wrap-up summary.

6. **Optional insertion**
   - Always mention: "Need to add a phase? Use `$phase-insert`."

7. **Write routing log**
   - Append a short entry to `planning/ROUTING.md` each time you route:
     - Timestamp, phase, step, reason, next command
   - Use `references/routing-template.md` for the log format.

## Example Usage

Prompt: `Use $phase-orchestrator to tell me the next command for the current phase.`

## Output Rules

- Always include a clear **Next step** line with the exact command.
- Always include `Current state` as concise bullets.
- Always include `Next action reason` as 1-3 concise bullets.
- Never output only the next command without state/reason context.
- Keep output short and direct.
