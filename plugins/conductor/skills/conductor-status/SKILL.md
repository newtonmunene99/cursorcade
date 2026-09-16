---
name: conductor-status
description: Display project and track progress
---

# Conductor Status

## Cursor Tool Mapping

- **AskQuestion** for structured user prompts (replaces Gemini `ask_user`)
- **Write** / **StrReplace** for file operations (replaces `write_file` / `replace`)
- **Shell** for shell commands (replaces `run_shell_command`)
- Use relative paths under `conductor/` for all Conductor artifacts

## Output Style

Follow **Agent Output Style** in the Conductor rule — **i-have-adhd** skill for base rules; `templates/output-style.md` for status format.

**Status-specific:** Line 1 = next action (`/conductor-implement` or named track). Then max 6 bullets: in-progress track + task N/M, progress fraction, **Eligible** tracks (mark ∥ when parallel-ready), **Blocked** tracks with missing `depends_on` (omit if none), verdict (On track | Behind | Blocked).

Apply **Eligible Tracks Protocol** from the Conductor rule when any track has `depends_on` or `programme_id` in metadata.

## Plugin Template Path

Locate installed plugin templates in this order:
1. `~/.cursor/plugins/local/conductor/templates/`
2. Search `~/.cursor/plugins/cache/` for the conductor plugin `templates/` directory
3. Fallback (marketplace dev): `./plugins/conductor/templates/` from repo root

## Parsing Cursor Plans

Use the **Deterministic Plumbing Protocol** in the Conductor rule — status is plumbing, not judgment:

1. `python3 <conductor_state.py> tracks` → registry, eligible / parallel-ready / blocked, recommended next track.
2. For each incomplete track with a `plan` path: `python3 <conductor_state.py> plan <plan>` → `counts`, `in_progress`, `next`, `phases`.
3. Only compose the summary from those JSON fields. Do not open plan files with the model unless the script fails (then fall back to counting `status:` lines by hand).

Per **Model Routing**, this skill is a cheap-model candidate: the only judgment is the one-word verdict.


## 1.0 SYSTEM DIRECTIVE
You are an AI agent. Your primary function is to provide a status overview of the current tracks file. This involves reading the **Tracks Registry** file, parsing its content, and summarizing the progress of tasks.

CRITICAL: Validate the result of every tool call. On failure, classify it with the **Failure Policy** in the Conductor rule and apply that row (retry once, skip with a note, repair, isolate, or escalate). Halt only where the policy says **Stop**; never abort unrelated work because one step failed.

---


## 1.1 SETUP CHECK
**PROTOCOL: Verify that the Conductor environment is properly set up.**

1.  **Verify Core Context:** Using the **Universal File Resolution Protocol**, resolve and verify the existence of:
    -   **Tracks Registry**
    -   **Product Definition**
    -   **Tech Stack**
    -   **Workflow**

2.  **Handle Failure:**
    -   If ANY of these files are missing, you MUST halt the operation immediately.
    -   Announce: "Conductor is not set up. Please run `/conductor-setup` to set up the environment."
    -   Do NOT proceed to Status Overview Protocol.

---

## 2.0 STATUS OVERVIEW PROTOCOL
**PROTOCOL: Follow this sequence to provide a status overview.**

### 2.1 Read Project Plan
1.  **Run the scripts** per **Parsing Cursor Plans** above (`tracks`, then `plan` for each incomplete track). Both handle the standard `- [ ] **Track:` and legacy `## [ ] Track:` formats.
2.  **Fallback only:** if the script errors, resolve the **Tracks Registry** and each **Cursor plan file** via the **Universal File Resolution Protocol** and count todo `status` values by hand.

### 2.2 Parse and Summarize Plan
1.  **Parse Content:** From the JSON: `phases`, `counts`, `in_progress`, `next`.
2.  **Generate Summary:** Create a concise summary of the project's overall progress. This should include:
    -   The total number of major phases.
    -   The total number of tasks.
    -   The number of tasks completed, in progress, and pending.

### 2.3 Present Status Overview
1.  **Compute dependency state:** For programme tracks, apply **Eligible Tracks Protocol** in the Conductor rule. Identify **eligible**, **parallel-ready** (lowest shared `order`), and **blocked** tracks.
2.  **Output Summary:** Follow **Output Style** above. Required fields:
    -   **Next action:** `/conductor-implement` when eligible tracks exist; name the recommended track (lowest `order`). If none eligible, say which blocker to clear first.
    -   **In progress:** `[~]` track — task N/M (or "none")
    -   **Progress:** tasks_completed/tasks_total across active programme or all tracks (percentage)
    -   **Eligible:** comma-separated track ids/descriptions; append `(∥)` when multiple parallel-ready
    -   **Blocked:** `<track>` waits on `<depends_on>` — omit section if none
    -   **Verdict:** On track | Behind | Blocked (one word + optional reason)
    -   Include current timestamp on its own line after the next-action line
3.  **Programme table:** When `tracks.md` has a sequencing table, one line: "See sequencing table in tracks.md for order."


