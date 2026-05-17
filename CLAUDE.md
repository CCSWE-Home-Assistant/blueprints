# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

Home Assistant blueprint definitions — reusable YAML templates a Home Assistant user can import and instantiate. No build, lint, or test tooling: a blueprint is "valid" when Home Assistant successfully imports it from its `source_url`.

Two top-level directories matching Home Assistant's blueprint domains:

- `automation/` — event-triggered blueprints (`triggers:` + `action:`)
- `script/` — manually-invokable blueprints (`sequence:`)

## File-path / source_url contract

Every blueprint's `source_url` points to its location in the public repo:

```
https://github.com/CCSWE-Home-Assistant/blueprints/<domain>/<filename>.yaml
```

When **renaming, moving, or adding** a blueprint, update `source_url` to match the new path. Home Assistant uses `source_url` to fetch updates, so a mismatch silently breaks the update flow for users who already imported the blueprint.

## Conventions used across these blueprints

- `author: Cory Charlton` on every blueprint.
- `mode: restart` + `max_exceeded: silent` is the default for automations (the `lock_down_television` automation uses `mode: single` because re-triggering during the TTS/delay window would be wrong).
- Inputs declared with `!input <name>`, then re-bound into `variables:` when the value is referenced inside a Jinja `value_template`. Direct `!input` works as an action argument but not inside `{{ ... }}` expressions — that's why you see the variables block.
- `selector.entity.filter[].domain` restricts the picker to one HA domain (`light`, `switch`, `remote`, `tts`, `media_player`, etc.). Use `multiple: true` when the action operates on a group.
- `area_id` selectors target whole areas — used in the scripts to act on every fan/light/switch in a room without enumerating entities.

## Verifying changes

There is no local validator. Sanity-check edits by:

1. Reading the file back and confirming indentation/`!input` references resolve.
2. Comparing against a sibling blueprint of the same domain — they share the same shape.

Actual validation happens when Home Assistant imports the blueprint via its `source_url`.
