# Conductor Agent Output Style

Conductor-specific output formats. **Base rules** (action first, numbered steps, restate state, no preamble/closers, etc.) live in the **[i-have-adhd](../../i-have-adhd/skills/i-have-adhd/SKILL.md)** skill from the Cursorcade marketplace — resolve that skill when the plugin is installed.

If **i-have-adhd** is not installed, apply the **Agent Output Style** summary in the Conductor rule (`rules/conductor.mdc`).

Run `/i-have-adhd` once per session for session-wide formatting on non-Conductor work too.

## Command-specific formats

### `/conductor-status`

Line 1: **Next action** (command + track/task).

Then (max 5 bullets):

- **Track:** `[~]` track name — task N/M
- **Progress:** completed/total (percent)
- **Blockers:** only if any
- **Verdict:** On track | Behind | Blocked

### `/conductor-implement`

Each progress update:

1. **Done:** what now works (one line)
2. **State:** task N/M, track name
3. **Next:** the upcoming todo or command

On track complete: lead with what shipped, then cleanup choice via `AskQuestion` only.

### `/conductor-review`

Line 1: **Verdict:** `Approve` | `Approve with nits` | `Request changes` — one-sentence reason.

Then structured report (Summary, Spec Coverage, Findings). Critical/High findings max 5 in chat; link to full list if more.

### `/conductor-setup` and `/conductor-new-track`

Line 1: current setup step and what the user should do (answer the `AskQuestion` prompt, or run a command).

Skip welcome preambles. State step N of total when resuming.

### `/conductor-revert`

Line 1: target (track/phase/task) and commit count.

Numbered execution plan before any git write. Confirm via `AskQuestion` only.

## Plan and spec authoring

When writing plans and specs (see `plan-authoring-guide.md`):

- Todo `content` strings start with a verb ("Write failing tests...", not "Tests for...")
- Plan body micro-steps are numbered, one action each
- Acceptance criteria are testable one-liners, max 5 per section before splitting

## Structured prompts

Use `AskQuestion` for structured prompts; do not repeat the same question in chat.

Pre-send check: follow **i-have-adhd** skill § Pre-send check when installed; otherwise verify first and last lines answer (a) what to do next and (b) what just happened.
