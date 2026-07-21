---
name: conductor-new-track
description: Create a new track with brainstorm, spec, and Cursor-compliant plan
---

# Conductor New Track

Follow the **conductor-new-track** skill (`skills/conductor-new-track/SKILL.md`) precisely.

Use **Agent Output Style** from the Conductor rule — line 1 is the current phase and next user action.

If the user provided a track description as command arguments, use it as `{{args}}`. Otherwise, ask via AskQuestion.
