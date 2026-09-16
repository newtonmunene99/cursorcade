---
name: conductor-status
description: Display current project and track progress
---

# Conductor Status

Follow the **conductor-status** skill (`skills/conductor-status/SKILL.md`) precisely.

Use **Agent Output Style** from the Conductor rule — line 1 is the next action; max 5 status bullets.

Run `conductor_state.py tracks` and `plan` (Deterministic Plumbing Protocol in the Conductor rule) for registry and todo progress; do not parse `conductor/` files with the model.
