# PLA Strong Figure – 0.4 mm nozzle, 0.20 mm layer

A strength-oriented version of the standard Bambu P2S PLA setup. It makes
slender parts print nearly solid, makes infill equally strong in every
direction, and improves the bond between layers, while keeping the standard
0.20 mm layer height, speeds and cooling, so surface quality and overhangs
are unchanged.

## Bambu Studio presets

The profile is two Bambu Studio user presets, both named **PLA Strong
Figure – 0.4 mm nozzle, 0.20 mm layer**. The preset files in this folder are
the main copies:

| Dropdown | Inherits from | Preset file (this folder) | Installed copy |
|---|---|---|---|
| Process | `0.20mm Standard @BBL P2S` | `pla-strong-figure-0.4mm-nozzle-0.20mm-layer.process.json` | `~/Library/Application Support/BambuStudio/user/<user-id>/process/PLA Strong Figure – 0.4 mm nozzle, 0.20 mm layer.json` |
| Filament | `Generic PLA @BBL P2S` | `pla-strong-figure-0.4mm-nozzle-0.20mm-layer.filament.json` | `~/Library/Application Support/BambuStudio/user/<user-id>/filament/PLA Strong Figure – 0.4 mm nozzle, 0.20 mm layer.json` |

They are already installed on this machine. To install them elsewhere, or
again after deleting them, use **File > Import > Import Configs...** in Bambu
Studio and select both `.json` files. Then pick the profile name in the
Process and Filament dropdowns.

Each preset stores only the parameters listed below and inherits the rest.
The filament preset is also limited to the `Bambu Lab P2S 0.4 nozzle`
printer (`compatible_printers`), whereas the base Generic PLA preset also
allows the 0.6 and 0.8 mm nozzles.

## Default profile it overrides

This profile starts from the default setup of this folder and changes only
the parameters listed below. Everything else stays at the system preset
values.

| Preset | Default (Bambu system preset) | Overridden? |
|---|---|---|
| Printer | `Bambu Lab P2S 0.4 nozzle` | No |
| Process | `0.20mm Standard @BBL P2S` | Yes, 5 parameters |
| Filament | `Generic PLA @BBL P2S` | Yes, 1 parameter |

## Nozzle

Designed for and verified with a **0.4 mm nozzle** only (`nozzle_diameter` =
0.4; the process preset is compatible only with `Bambu Lab P2S 0.4 nozzle`).
The values below come from the standard 0.4 mm presets and are not
overridden:

| Parameter | Value |
|---|---|
| `layer_height` / `initial_layer_print_height` | 0.20 mm / 0.20 mm |
| `outer_wall_line_width` | 0.42 mm |
| `inner_wall_line_width` | 0.45 mm |
| `sparse_infill_line_width` | 0.45 mm |
| `line_width`, `top_surface_line_width`, `internal_solid_infill_line_width`, `support_line_width` | 0.42 mm |
| `initial_layer_line_width` | 0.50 mm |

The wall and shell counts below are set for these widths. With 4 wall loops,
each side gets 0.42 + 3 × 0.45 ≈ **1.8 mm** of wall, and 4 bottom layers give
a 0.8 mm solid sole. Don't use this profile unchanged with another nozzle:
the real target is about 1.8 mm of wall, so set the wall count to match
(about 3 loops on a 0.6 mm nozzle, about 8 on a 0.2 mm nozzle). Also re-check
any thin details sized for 0.4 mm lines.

## When to use it

Use it for display figures and statues that are handled, moved or might be
knocked over, especially slender ones: standing on two thin legs, with
free-hanging arms, cords, chains or other thin details.

Don't use it for:

- Small or flat parts where the default profile is already solid enough. It
  costs about 30% more filament and 15% more time.
- Functional parts under sustained load or heat. Use PETG/ABS/ASA instead:
  PLA softens at about 55–60 °C whatever the settings.
- Prints with large flat tops or very sharp overhangs where cooling matters
  more. The higher nozzle temperature can slightly worsen stringing and
  bridges.

## Changed parameters

### Process: `0.20mm Standard @BBL P2S`

| Parameter (Bambu Studio label) | Original | Final | Why |
|---|---|---|---|
| `wall_loops` (Wall loops) | 2 | 4 | Walls carry most of the load in bending and impacts. With 4 walls (about 1.8 mm per side), narrow parts like ankles, wrists and cords print almost solid instead of a thin shell around sparse infill. The biggest strength gain. |
| `sparse_infill_density` (Sparse infill density) | 15% | 20% | Firmer core in thick sections and better support under top surfaces. Above 20–25% you mostly add weight and time for little extra strength. |
| `sparse_infill_pattern` (Sparse infill pattern) | grid | gyroid | Equally strong in every direction and interlocks between layers, which suits falls and handling. Grid is strongest only along its two line directions and runs the nozzle over its own lines on every layer. |
| `bottom_shell_layers` (Bottom shell layers) | 3 | 4 | The feet or base are the only contact with the plate and carry the whole figure. A thicker solid sole adds stiffness and gives the brim a stronger first layer to hold. |
| `wall_generator` (Wall generator) | classic | arachne | Arachne varies line width, so features thinner than two full lines still print as solid lines. Classic leaves gaps in them or skips them. |

### Filament: `Generic PLA @BBL P2S`

| Parameter (Bambu Studio label) | Original | Final | Why |
|---|---|---|---|
| `nozzle_temperature` (Nozzle, other layers) | 220 °C | 230 °C | Hotter PLA flows more and fuses better with the layer below. PLA is weakest between layers, so this gives meaningfully stronger layer bonds. Within the preset's 190–240 °C range. The first layer stays at 220 °C. |

Supports are not part of this profile: turn them on per model as needed.

## Models using this profile

- `ironman-urban.3mf`: the geometry is also reinforced (thicker ankles,
  drawstrings, chain and laces). The project selects both presets of this
  profile directly, so Bambu Studio shows them unmodified. Its tree supports
  (supports may start on the model) are a per-object setting on the figure,
  not part of the profile.
