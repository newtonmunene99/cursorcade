# Conductor Plugin

Context-driven development for Cursor: setup, spec, plan, implement, review, and revert.

**Measure twice, code once.**

## Commands

| Command | Description |
| :------ | :---------- |
| `/conductor-setup` | One-time project bootstrap |
| `/conductor-new-track` | Brainstorm, spec, plan (single track or **programme mode**) |
| `/conductor-implement` | Execute plan todos (respects `depends_on`) |
| `/conductor-status` | Show progress |
| `/conductor-revert` | Git-aware revert |
| `/conductor-review` | Review against guidelines, plan, spec |
| `/conductor-programme-review` | Review multi-track programme |
| `/conductor-validate-review` | Validate review findings against repo |
| `/conductor-prototype` | Decision-track spike on `spike/<slug>` branch |

## Programme mode

From `.cursor/reviews/*.md` → validate → split tracks → synthesis → implement in order.

Reference: `docs/examples/remediation-programme-example.md`

## Decision tracks

Deliverable is an **OKF concept** in a **repo knowledge bundle**:

| Scope | Bundle root | Example deliverable |
| ----- | ----------- | ------------------- |
| Domain package | `<pkg>/knowledge/` | `<pkg>/knowledge/decisions/<slug>.md` |
| Repository | `knowledge/` | `knowledge/decisions/<slug>.md` |

Workflow: `/grilling` → `/research` → `/conductor-prototype` → `/grill-with-docs`

See [OKF v0.1](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md).

## Project docs / knowledge requests

1. Discover existing `**/knowledge/index.md` bundles
2. Prefer repo-root `knowledge/` or domain `<pkg>/knowledge/` beside code
3. Scaffold from `templates/knowledge/bundle-placement-guide.md`

## Artifacts

- **Conductor:** `.cursor/context/`, `.cursor/specs/`, `.cursor/plans/`, `.cursor/reviews/`
- **OKF knowledge:** `knowledge/` or `<pkg>/knowledge/` in the repository

## Attribution

Conductor from [gemini-cli-extensions/conductor](https://github.com/gemini-cli-extensions/conductor). OKF from [Google Cloud OKF spec](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md). Engineering skills from [mattpocock/skills](https://github.com/mattpocock/skills) (MIT).

**Output style:** Base rules come from **[i-have-adhd](../i-have-adhd/)** (`skills/i-have-adhd/SKILL.md`). Conductor-only command formats live in `templates/output-style.md`. Install both plugins from the marketplace; run `/i-have-adhd` for session-wide formatting on non-Conductor work.
