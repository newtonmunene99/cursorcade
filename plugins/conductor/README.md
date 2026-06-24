# Conductor Plugin

Context-driven development for Cursor: setup, spec, plan, implement, review, and revert.

**Measure twice, code once.**

## Install

Add the [Cursorcade](https://github.com/newtonmunene99/cursorcade) marketplace, then install **conductor**:

```text
/add-plugin https://github.com/newtonmunene99/cursorcade
```

Then install **conductor** from the marketplace panel.

## Uninstall

```text
/remove-plugin conductor
```

This unregisters the plugin from Cursor. Project files under `.cursor/` are not removed.

Alternatively: **Dashboard → Settings → Plugins** → disable or uninstall **conductor**.

For local symlinks: `rm ~/.cursor/plugins/local/conductor`, then reload Cursor.

## Commands

| Command | Description |
| :------ | :---------- |
| `/conductor-setup` | One-time project bootstrap |
| `/conductor-new-track` | Brainstorm, spec, and detailed Cursor plan for a new track |
| `/conductor-implement` | Execute plan todos |
| `/conductor-status` | Show progress |
| `/conductor-revert` | Git-aware revert |
| `/conductor-review` | Review against guidelines, plan, spec, and documentation |

## What you get

- Persistent context via `rules/conductor.mdc`
- Project artifacts under `.cursor/context/`, `.cursor/specs/`, `.cursor/plans/`
- Cursor-native plan frontmatter with `todos` and `status` fields

## Templates

Bundled under `templates/`:

- `workflow.md` — Default TDD workflow (copied to `.cursor/context/workflow.md` on setup)
- `plan-authoring-guide.md` — Plan quality rules and mandatory sync todos (plugin reference, not copied on setup)
- `code_styleguides/` — Language style guides (copied to `.cursor/context/code_styleguides/`)

When running setup, locate templates from the installed plugin path (`~/.cursor/plugins/local/conductor/templates/` or plugin cache).

## Attribution

Conductor is derived from [gemini-cli-extensions/conductor](https://github.com/gemini-cli-extensions/conductor), the original Conductor extension for Gemini CLI. That project acknowledges [Keith Ballington's .conductor](https://github.com/keithballinger/.conductor) as groundwork.

This Cursor adaptation repackages Conductor as a plugin (rules, skills, commands) with project artifacts under `.cursor/context/`, `.cursor/specs/`, and `.cursor/plans/`.
