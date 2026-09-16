# Cursorcade

**A curated arcade of Cursor plugins for developers who mean business.**

This repository is a [Cursor plugin marketplace](https://cursor.com/docs/reference/plugins#multi-plugin-repositories). Add it once in Cursor, then install individual plugins from the marketplace panel.

## Install

In Cursor chat, add this repository as a plugin marketplace:

```text
/add-plugin https://github.com/newtonmunene99/cursorcade
```

Then install the plugins you want from the marketplace panel.

### Dashboard (alternative)

1. Go to **Dashboard → Settings → Plugins → Add Marketplace**
2. Import `https://github.com/newtonmunene99/cursorcade`
3. Install plugins from the marketplace list

## Available plugins

| Plugin | Description |
| :----- | :---------- |
| [**conductor**](plugins/conductor/) | Context-driven development: setup, spec, plan, implement, review, programmes, decision tracks |
| [**engineering**](plugins/engineering/) | Grilling, research, prototype, architecture review (pairs with Conductor decision tracks) |
| [**i-have-adhd**](plugins/i-have-adhd/) | Session-wide ADHD-friendly output — action first, numbered steps ([upstream](https://github.com/ayghri/i-have-adhd)) |

See each plugin's README for commands, usage, and uninstall instructions.

## Conductor project artifacts

Conductor stores project state under `conductor/` at the repo root (shared layout with [gemini-cli-extensions/conductor](https://github.com/gemini-cli-extensions/conductor) and [claudekit](https://github.com/newtonmunene99/claudekit)):

```text
conductor/
├── context/          # product, workflow, tracks registry
├── specs/<track_id>/ # track specifications
├── plans/            # implementation plans
├── reviews/          # programme reviews
└── archive/          # completed tracks
```

## Local development

Symlink a plugin for local testing:

```bash
ln -s /path/to/cursorcade/plugins/conductor ~/.cursor/plugins/local/conductor
```

Reload Cursor (**Developer: Reload Window**).

To remove a local symlink:

```bash
rm ~/.cursor/plugins/local/conductor
```

## Repository layout

```text
cursorcade/
├── .cursor-plugin/
│   └── marketplace.json       # Marketplace manifest
├── plugins/
│   └── conductor/             # Conductor plugin
│       ├── .cursor-plugin/
│       │   └── plugin.json
│       ├── rules/
│       ├── scripts/               # conductor_state.py — deterministic registry / plan / path checks
│       ├── skills/
│       ├── commands/
│       └── templates/
└── scripts/
    └── validate-template.mjs
```

## Validate

```sh
node scripts/validate-template.mjs
```

## Adding a plugin

1. Create `plugins/<plugin-name>/` with a `.cursor-plugin/plugin.json` manifest
2. Add rules, skills, commands, or other components under that folder
3. Register the plugin in `.cursor-plugin/marketplace.json`
4. Run `node scripts/validate-template.mjs`

## License

Apache License 2.0 — see [LICENSE](LICENSE).
