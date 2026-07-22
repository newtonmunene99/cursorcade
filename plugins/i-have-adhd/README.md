# I Have ADHD

ADHD-friendly agent output for Cursor: **action first**, numbered steps, state restated every turn, no buried answers.

Vendored from [ayghri/i-have-adhd](https://github.com/ayghri/i-have-adhd) (MIT). Loosely based on _The Adult ADHD Tool Kit_ (Ramsay & Rostain).

## Install

```text
/add-plugin https://github.com/newtonmunene99/cursorcade
```

Install **i-have-adhd** from the Cursorcade marketplace panel.

## Usage

```text
/i-have-adhd
```

Rules apply for the **rest of the session**. Turn off with "stop adhd mode" or "normal mode".

## Relationship to Conductor

[Conductor](../conductor/) references this skill for base output rules. Conductor-only command formats (status layout, review verdict line, etc.) stay in Conductor's `templates/output-style.md`. Install both plugins; run `/i-have-adhd` for session-wide formatting beyond Conductor commands.

## Upstream

Track changes at [github.com/ayghri/i-have-adhd](https://github.com/ayghri/i-have-adhd). To update this vendored copy, diff against upstream `skills/i-have-adhd/SKILL.md`.

## License

MIT — see upstream repository.
