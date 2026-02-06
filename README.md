# codex-plan-mode-skill

A bundled skill set for phased planning and execution in Codex.

This repository includes:
- `plan-mode`
- `phase-orchestrator`
- `phase-discuss`
- `phase-plan`
- `phase-execute`
- `phase-insert`

## What It Does

- Creates decision-complete plans with optional integrated research.
- Splits work into phases (`01`, `02`, `03`, optional inserts like `02.1`).
- Routes through a repeatable flow:
  1. `plan-mode`
  2. `phase-orchestrator`
  3. `phase-discuss`
  4. `phase-plan`
  5. `phase-execute`
- Maintains planning artifacts under `planning/`.

## Artifact Contract

Expected files:
- `planning/PLAN.md`
- `planning/RESEARCH.md` (when research is enabled)
- `planning/ROADMAP.md`
- `planning/STATE.md`
- `planning/ROUTING.md`
- `planning/phases/<phase-dir>/CONTEXT.md`
- `planning/phases/<phase-dir>/PLAN.md`
- `planning/phases/<phase-dir>/SUMMARY.md`

State is tracked as strict YAML in `planning/STATE.md` with canonical `phase_dir` to avoid slug drift.

## Install

Copy skill folders into your Codex skills directory:

```bash
cp -R skills/* "$CODEX_HOME/skills/"
```

Or copy specific skills only:

```bash
cp -R skills/plan-mode "$CODEX_HOME/skills/"
cp -R skills/phase-orchestrator "$CODEX_HOME/skills/"
cp -R skills/phase-discuss "$CODEX_HOME/skills/"
cp -R skills/phase-plan "$CODEX_HOME/skills/"
cp -R skills/phase-execute "$CODEX_HOME/skills/"
cp -R skills/phase-insert "$CODEX_HOME/skills/"
```

## Usage

### 1) Create the plan

```text
Use $plan-mode to plan <project or feature>.
```

### 2) Route to next step

```text
Use $phase-orchestrator to guide the next phase action.
```

### 3) Run phase flow

```text
Use $phase-discuss 01
Use $phase-plan 01
Use $phase-execute 01
```

### 4) Insert urgent work (optional)

```text
Use $phase-insert to add a phase after 02 named "Payments" with goal "Integrate Stripe".
```

## Notes

- Popup selection via `request_user_input` is preferred where available.
- Fallback behavior uses plain-text multiple-choice questions when popup tool is unavailable.
- `phase-plan`, `phase-execute`, and `phase-orchestrator` are configured to output both status and substance (what was planned/executed and what happens next).
