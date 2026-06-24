# Conductor Plan Authoring Guide

Use this guide when generating Cursor plan files (`.cursor/plans/*.plan.md`) during `/conductor-new-track`, `/conductor-setup`, or when revising an existing track plan.

Techniques adapted from [Superpowers](https://github.com/obra/superpowers) (MIT).

## Plan Header (markdown body)

Every plan body MUST start with:

```markdown
> **Conductor plan:** Runnable directly in Cursor or via `/conductor-implement`.
> Follow `.cursor/context/workflow.md` and the Git Write Policy in the Conductor rule.
```

Below the header, include:

- **Goal** — one sentence
- **Architecture** — 2–3 sentences
- **File structure map** — files to create/modify with one-line responsibility each

## Mandatory sync todos (frontmatter)

Every plan frontmatter MUST include these bookend todos (in addition to phase verification todos when the workflow defines them):

| Order | id | content (summary) |
| ----- | --- | ----------------- |
| **First** | `conductor-sync-in-progress` | Mark track `[~]` in `tracks.md`, set `metadata.json` `status` to `in_progress`, update `updated_at`. Git Write Policy applies. |
| **Last** | `conductor-sync-complete` | Mark track `[x]` in `tracks.md`, set `metadata.json` `status` to `completed`, update `updated_at`, commit Conductor files. Git Write Policy applies. |

**Do NOT inject** feature-branch, worktree, or other git-isolation todos by default. Git workflow is chosen at implementation start via the **Git Isolation Protocol** in the Conductor rule — unless the user explicitly asked to bake a git workflow into the plan during new-track planning.

### Example frontmatter

```yaml
---
name: Track Title
overview: One-paragraph summary derived from spec
todos:
  - id: conductor-sync-in-progress
    content: "Conductor — Mark track in progress (tracks.md [~], metadata.json in_progress)"
    status: pending
  - id: example-task
    content: "Write failing tests for example feature"
    status: pending
  - id: conductor-sync-complete
    content: "Conductor — Mark track complete (tracks.md [x], metadata.json completed)"
    status: pending
isProject: true
---
```

## Task right-sizing

Each frontmatter todo is the smallest unit that:

- Has its own test cycle (when workflow uses TDD)
- Produces an independently verifiable deliverable
- Aligns with the project's **Workflow** (e.g., separate "Write Tests" and "Implement" todos when TDD is required)

Fold setup, configuration, and scaffolding into the todo whose deliverable needs them. Split only where a reviewer could reject one task while approving its neighbor.

## Plan body detail (per task)

For each todo, the markdown body MUST include a section with:

- **Files:** `Create`, `Modify`, and `Test` paths (exact paths)
- **Interfaces:** what this task consumes from earlier tasks and what later tasks rely on (signatures, types, function names)
- **Micro-steps** (when workflow uses TDD), as checkboxes:
  - Write failing test
  - Run test — verify it fails for the expected reason
  - Implement minimal code
  - Run test — verify pass
  - Commit (Git Write Policy applies)
- **Commands** with expected output where applicable

## No placeholders

These are plan failures — never write them:

- TBD, TODO, "implement later", "fill in details"
- "Add appropriate error handling" / "add validation" / "handle edge cases" without specifics
- "Write tests for the above" without actual test code
- "Similar to Task N" (repeat the code — tasks may be read out of order)
- Steps that describe what to do without showing how (code blocks required for code steps)

## Plan self-review

After drafting the complete plan, run this checklist and fix inline:

1. **Spec coverage:** Each spec requirement maps to at least one todo.
2. **Placeholder scan:** No banned patterns above.
3. **Type consistency:** Signatures and names match across tasks.
4. **Sync bookends:** `conductor-sync-in-progress` is first; `conductor-sync-complete` is last.
5. **Phase todos:** Manual verification todos exist for each phase when the workflow defines phase checkpointing.

## Direct plan execution

Users may run this plan from Cursor chat without `/conductor-implement`. Sync bookend todos keep `tracks.md` and `metadata.json` aligned with progress. Implementation todos follow `.cursor/context/workflow.md` including TDD and Systematic Debugging Protocol.
