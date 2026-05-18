# home-assistant-blueprints

A small collection of [Home Assistant](https://www.home-assistant.io/) [blueprints](https://www.home-assistant.io/docs/automation/using_blueprints/) — reusable templates for automations and scripts.

## Importing a blueprint

Click an **Import Blueprint** badge below, or in Home Assistant go to **Settings → Automations & Scenes → Blueprints → Import Blueprint** and paste the blueprint's URL.

## Automations

### Automatically Turn Off Lights

Turns off the selected lights after a configurable delay (default 15 minutes). The timer resets each time any of the lights changes state, so it always counts from the most recent activity.

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FCCSWE-Home-Assistant%2Fblueprints%2Fautomation%2Fauto_turn_off_light.yaml)

### Duck Media Volume During Voice

Lowers the volume of selected media players when a Home Assistant Voice satellite starts listening, and restores them when it returns to idle. Players that don't support setting a specific volume are paused if possible (e.g. Android TV via its `media_player.*` entity playing a Cast video), or muted as a last resort. Idle, paused, and already-muted players are left alone. Includes a safety timeout so a satellite that gets stuck in the `responding` state can't strand the state.

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FCCSWE-Home-Assistant%2Fblueprints%2Fautomation%2Fduck_volume_for_voice.yaml)

### Lock Down Television

Watches a TV remote entity for turning on and shuts it back off after a configurable delay. Optionally plays a TTS announcement on a media player before powering off — useful as a "time's up" guard for a kid's TV.

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FCCSWE-Home-Assistant%2Fblueprints%2Fautomation%2Flock_down_television.yaml)

### Synchronize Light and Light

Keeps two lights in lock-step: when one turns on/off or its brightness changes, the other follows.

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FCCSWE-Home-Assistant%2Fblueprints%2Fautomation%2Fsynchronize_light_light.yaml)

### Synchronize Light and Switch

Keeps a light and a switch in sync — toggling either one drives the other. Useful when a smart bulb and a smart switch control the same fixture.

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FCCSWE-Home-Assistant%2Fblueprints%2Fautomation%2Fsynchronize_light_switch.yaml)

## Scripts

### Turn On Area

Turns on every fan, light, and switch in a selected area. Each device class can be toggled off via the blueprint inputs.

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FCCSWE-Home-Assistant%2Fblueprints%2Fscript%2Fturn_on_area.yaml)

### Turn Off Area

Mirror of *Turn On Area* — turns off every fan, light, and switch in a selected area, with per-class toggles.

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FCCSWE-Home-Assistant%2Fblueprints%2Fscript%2Fturn_off_area.yaml)

## License

Released under the [MIT License](LICENSE).
