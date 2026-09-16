---
name: conductor-validate-review
description: Validate a code review document against the repository before creating a remediation programme
---

# Conductor Validate Review

## Cursor Tool Mapping

- **AskQuestion** for structured user prompts
- **Write** / **StrReplace** for validation output
- **Shell** for read-only verification (`test -f`, `rg`, `git grep`)
- Use relative paths under `conductor/` for Conductor artifacts

## Output Style

Follow **Agent Output Style** in the Conductor rule. Line 1 = validation status and count of findings checked.

## Plugin Template Path

Locate installed plugin templates:
1. `~/.cursor/plugins/local/conductor/templates/`
2. Search `~/.cursor/plugins/cache/` for conductor `templates/`
3. Fallback: `./plugins/conductor/templates/` from repo root

## 1.0 SYSTEM DIRECTIVE

Validate a review document in **Reviews Directory** against the codebase. Produce a validation addendum before `/conductor-new-track` programme creation.

**Read-only** until writing validation output. Git write operations follow **Git Write Policy**.

---

## 2.0 VALIDATION PROTOCOL

### 2.1 Resolve review

1. If `{{args}}` is non-empty, use as review path (relative to repo root or under `conductor/reviews/`).
2. Otherwise `AskQuestion` — header "Review path", type text, placeholder `conductor/reviews/foo_YYYYMMDD-review.md`.
3. Verify file exists via Universal File Resolution.

### 2.2 Extract findings

Parse review for:

- **Location:** lines with file paths and line numbers
- **Severity** claims (Critical, High, ARCH-N, §X.Y)
- Inline backtick paths in finding bodies

Load `templates/review-document-header.md` for expected structure.

### 2.3 Verify paths

1. Run `python3 <conductor_state.py> verify-paths <review path>` (**Deterministic Plumbing Protocol** in the Conductor rule). It checks every cited path and `file:line` reference and suggests corrections for missing files.
2. For paths marked `verified` with a line ref, `Read` the cited lines to confirm the symbol/context still matches the claim.
3. Record: **Verified** | **Wrong path** (use the script's `suggestions`) | **Not found**
4. Fallback without `python3`: `test -f` / `test -d`, glob variants, and `Read` for line refs.

### 2.4 Verify severity claims

For each finding with code citation:

1. Read cited code
2. Mark: **Confirmed** | **Partially confirmed** | **Disputed** | **Obsolete**
3. Drop claims you cannot validate — do not flag uncertain items

### 2.5 Write validation addendum

Default: sibling file `<review-stem>_validation.md` (preserves original review).

`AskQuestion` if user prefers append to review instead.

Addendum structure:

```markdown
# Validation — <review title>

**Source:** `conductor/reviews/<file>`
**Validated:** YYYY-MM-DD

## Path verification

| Finding | Cited path | Status | Correction |
| ------- | ---------- | ------ | ---------- |

## Severity confirmation

| ID | Verdict | Notes |
| -- | ------- | ----- |

## Blocking issues

List wrong paths or disputed Critical findings that must resolve before programme creation.

## Programme readiness

**Ready** | **Not ready** — <reason>
```

### 2.6 Halt rules

- **Blocking wrong paths** on Critical findings → announce halt; programme creation blocked until fixed
- User may opt out via `AskQuestion` when running `/conductor-new-track` (document opt-out in programme metadata)

### 2.7 Completion

Announce: "Validation written to `<path>`. Next: `/conductor-new-track` from this review."
