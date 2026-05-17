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

## Jinja in HA templates

Home Assistant uses vanilla Jinja2 (plus its own filter/global additions like `expand`, `states`, `state_attr`, `combine`). Two things this means in practice:

- **No comprehensions.** Jinja2 does not support list/dict/set comprehensions. Syntax like `[x for x in y]` or `{k: v for k in y}` fails to parse — HA surfaces this as `TemplateSyntaxError: expected token ',', got 'for'`.
- **Build collections with `namespace` + loop instead.** To produce a list or dict from an iterable inside `variables:` or any other template, accumulate into a `namespace` and emit it at the end:

  ```jinja
  {%- set ns = namespace(items=[]) -%}
  {%- for e in some_list -%}
    {%- if <predicate> -%}
      {%- set ns.items = ns.items + [{'key': e, 'val': state_attr(e, 'attr')}] -%}
    {%- endif -%}
  {%- endfor -%}
  {{ ns.items }}
  ```

  See `automation/duck_volume_for_voice.yaml` for a real example. Use `{%- -%}` whitespace stripping inside a folded `>-` scalar so the emitted value isn't surrounded by stray spaces.

## Detecting media_player capabilities

Attribute presence is **not** a reliable signal of which actions a `media_player.*` entity supports. The Android TV `media_player` entity, for example, exposes a `volume_level` attribute while *not* supporting `media_player.volume_set` — calling the action on it fails at runtime with `Entity ... does not support action media_player.volume_set`.

The authoritative check is the `supported_features` bitmask, tested with the `bitwise_and` filter (HA-provided, not vanilla Jinja2):

```jinja
{% set sf = state_attr(e, 'supported_features') | int(0) %}
{% if sf | bitwise_and(4) > 0 %}{# VOLUME_SET (bit 2) #}{% endif %}
{% if sf | bitwise_and(8) > 0 %}{# VOLUME_MUTE (bit 3) #}{% endif %}
```

Relevant `MediaPlayerEntityFeature` bit values:

- `1` PAUSE · `2` SEEK · `4` VOLUME_SET · `8` VOLUME_MUTE
- `16` PREVIOUS_TRACK · `32` NEXT_TRACK · `128` TURN_ON · `256` TURN_OFF
- `512` PLAY_MEDIA · `1024` VOLUME_STEP · `2048` SELECT_SOURCE · `4096` STOP · `8192` CLEAR_PLAYLIST · `16384` PLAY · `32768` SHUFFLE_SET · `65536` SELECT_SOUND_MODE · `131072` BROWSE_MEDIA · `262144` REPEAT_SET · `524288` GROUPING · `1048576` MEDIA_ANNOUNCE · `2097152` MEDIA_ENQUEUE · `4194304` SEARCH_MEDIA

Other domains have their own `<Domain>EntityFeature` enums with separate bit values — always check the right enum.

### Filtering the picker by supported_features

Entity selectors accept the same capability check at picker time via `filter[].supported_features`, using the dotted enum-path format:

```yaml
selector:
  entity:
    filter:
      - domain: media_player
        supported_features:
          - media_player.MediaPlayerEntityFeature.VOLUME_SET
      - domain: media_player
        supported_features:
          - media_player.MediaPlayerEntityFeature.VOLUME_MUTE
    multiple: true
```

Multiple filter dicts in the outer list are OR'd (entity matches if it satisfies *any* dict) — this is the documented way to express "supports A or B." Combining multiple features within one dict's `supported_features:` list isn't documented; prefer separate dicts for OR. Keep the runtime `bitwise_and` checks too as a defense against entities whose features change after selection.

## Verifying changes

There is no local validator. Sanity-check edits by:

1. Reading the file back and confirming indentation/`!input` references resolve.
2. Comparing against a sibling blueprint of the same domain — they share the same shape.

Actual validation happens when Home Assistant imports the blueprint via its `source_url`.
