# ui/climate

Thermostat tile with full-screen detail page. Tile shows current temperature and reflects active mode color. Detail page has a large arc for target temperature, mode selector (OFF / HEAT / COOL), and fan speed selector.

## Files

| File | Purpose |
|---|---|
| `local.yaml` | ESPHome climate component (defined in the same device YAML) |
| `remote.yaml` | Home Assistant climate entity |
| `detail.yaml` | Shared detail page — included automatically, do not include directly |

## Variables

| Variable | Required | Description |
|---|---|---|
| `uid` | ✅ | Unique identifier |
| `entity_id` | ✅ | ESPHome climate ID (local) or HA entity e.g. `"climate.living_room"` (remote) |
| `row` | ✅ | Grid row position (0-based) |
| `column` | ✅ | Grid column position (0-based) |
| `text` | ✅ | Label shown on tile |
| `icon` | — | MDI glyph (default: `$mdi_thermostat`) |
| `row_span` | — | Number of rows to span (default: `1`) |
| `column_span` | — | Number of columns to span (default: `1`) |
| `page_id` | — | Parent page ID (default: `main_page`) |

## Usage

```yaml
# Local — ESPHome climate component (e.g. pid_climate, bang_bang)
hvac_tile: !include
  file: esphome-modular-lvgl-buttons/ui/climate/local.yaml
  vars:
    uid: hvac
    entity_id: living_room_climate   # your climate: component id
    row: 0
    column: 0
    text: "Living Room"
    icon: $mdi_thermostat
    min_temp: 150    # 15.0°C
    max_temp: 300    # 30.0°C

# Remote — Home Assistant climate entity
ac_tile: !include
  file: esphome-modular-lvgl-buttons/ui/climate/remote.yaml
  vars:
    uid: bedroom_ac
    entity_id: "climate.bedroom_ac"
    row: 0
    column: 1
    text: "Bedroom"
    icon: $mdi_air_conditioner
    supports_heat: "false"   # cooling-only unit

# Heat-only thermostat (no fan)
boiler_tile: !include
  file: esphome-modular-lvgl-buttons/ui/climate/remote.yaml
  vars:
    uid: boiler
    entity_id: "climate.boiler"
    row: 1
    column: 0
    text: "Boiler"
    icon: $mdi_radiator
    supports_cool: "false"
    supports_fan: "false"
```

## Required glyphs

Add to your device `font:` block:

```
$mdi_thermostat   $mdi_fan   $mdi_chevron_left
$mdi_arrow_oscillating   $mdi_water_percent
```

## Detail page layout

- **Arc** — drag to set target temperature (disabled when OFF)
- **Mode label** — shows HEATING / COOLING / IDLE
- **Target temp** — large center label, shows "OFF" when mode is off
- **Current temp** — smaller label below target, in `misty_blue`
- **Bottom-left button** — opens mode dropdown (OFF / HEAT / COOL)
- **Bottom-right button** — opens fan dropdown (AUTO / LOW / MED / HIGH), color reflects active fan speed
- **Top-right button** — back to parent page
- **Tap outside dropdown** — dismisses dropdown

## Notes

- `min_temp` and `max_temp` are arc integer values = °C × 10, giving 0.1° arc resolution.
- The remote variant reads `temperature` attribute for target temp (single setpoint). For dual-setpoint (heat_cool mode) extend the remote with additional `target_temp_low`/`target_temp_high` sensors as needed.
- Fan dropdown is hidden entirely if `supports_fan: "false"`.
