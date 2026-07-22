---
name: conductor-prototype
description: Run a throwaway spike for a Conductor decision track on an isolated branch
---

# Conductor Prototype

## Cursor Tool Mapping

- **AskQuestion** for git workflow and branch confirmation
- **Write** / **StrReplace** for evidence capture
- **Shell** for git and run commands
- **Task** with `subagent_type=explore` when exploring spike context

## Output Style

Follow **Agent Output Style** in the Conductor rule. Line 1 = decision question being spiked and branch name.

## Plugin Template Path

**Conductor:** `~/.cursor/plugins/local/conductor/templates/` → cache → `./plugins/conductor/templates/`

**Engineering prototype:** `~/.cursor/plugins/local/engineering/skills/prototype/` → cache → `./plugins/engineering/skills/prototype/`

Resolve `LOGIC.md` and `UI.md` from engineering plugin for spike shape.

---

## 1.0 SYSTEM DIRECTIVE

Run a **throwaway prototype** for a Conductor **decision track**. Delegates spike mechanics to the engineering `prototype` skill; adds Conductor guardrails (branch isolation, OKF evidence capture, delete-before-complete).

Decision evidence is stored as OKF concepts at `<bundle-root>/decisions/evidence/<slug>.md`. Resolve `<bundle-root>` per **Knowledge Bundle Resolution** (e.g. `<pkg>/knowledge/`, repo `knowledge/`).

**Git Write Policy** applies to branch create/delete.

---

## 2.0 PROTOCOL

### 2.1 Resolve decision track

1. If `{{args}}` names a track, match against **Tracks Registry**.
2. Otherwise find track with `metadata.json` `"track_role": "decision"` that is `[~]` or `[ ]`.
3. Read track spec for the decision question and allowed production edits.

### 2.2 Pick prototype branch

From engineering `prototype/SKILL.md`:

- Logic/state question → **LOGIC.md**
- UI appearance question → **UI.md**

If ambiguous, `AskQuestion` — default logic for backend decision tracks.

### 2.3 Git isolation

1. Follow **Git Isolation Protocol** — propose branch `spike/<slug>` (slug from track id or decision title).
2. Create/switch via Git Write Policy approval.
3. Announce: spike is throwaway; nothing merges to main.

### 2.4 Run prototype

Execute engineering prototype rules:

1. Throwaway code clearly marked; locate near module under test
2. One command to run
3. No persistence unless question requires it
4. Surface full state after each action
5. Skip polish — no tests beyond runnable

If engineering plugin unavailable, follow `LOGIC.md` / `UI.md` inline from engineering plugin cache.

### 2.5 Capture verdict (OKF evidence concept)

Write `<bundle-root>/decisions/evidence/<slug>.md`:

```markdown
---
type: Decision Evidence
title: Spike — <question>
description: Verdict from /conductor-prototype
tags: [evidence, spike]
timestamp: YYYY-MM-DDTHH:MM:SSZ
decision: decisions/<slug>
---

# Spike evidence — <title>

**Track:** <track_id>
**Branch:** spike/<slug>
**Question:** <one sentence>

## Verdict

<plain answer the spike settled>

## Observations

<numbers, paths, what broke under each option>
```

Update `<bundle-root>/log.md` with an **Update** entry.

### 2.6 Delete spike

1. Switch off spike branch (Git Write Policy).
2. Delete branch; verify working tree clean except allowed paths (decision tracks may allow domain OKF bundles e.g. `pkg/knowledge/` — per spec).
3. Announce deletion complete.

### 2.7 Block sync-complete

Decision track `conductor-sync-complete` MUST NOT run while spike branch exists. Implement skill enforces: if `git branch --list 'spike/*'` non-empty, halt and run delete step.

### 2.8 Completion

Announce: "Spike verdict captured at `<bundle-root>/decisions/evidence/<slug>.md`. Next: `/grill-with-docs` for OKF decision concept."
