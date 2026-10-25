# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

# 3D models

Bambu Studio projects (`.3mf`), one per model, kebab-case names (e.g.
`lego-brick-mug.3mf`). Not a code repo: no source tree or tests.

- Repo root: the user's own models.
- `print-profiles/`: custom print profiles (see below).
- `third-party-models/`: downloaded models, gitignored (local only).
- `resources/brand/`: README banner.

## Printer setup

- **Bambu Lab P2S, 0.4 mm nozzle**, 256 x 256 mm bed.
- Default process **0.20mm Standard @BBL P2S**, filament **Generic PLA @BBL P2S**.

## Print profiles

A profile is any set of settings that differs from the defaults above.

- **Name:** `<Material> <Purpose> – <nozzle> mm nozzle, <layer> mm layer`
  (e.g. *PLA Strong Figure – 0.4 mm nozzle, 0.20 mm layer*). Files in
  `print-profiles/` use the kebab-case form `<file-name>` =
  `<material>-<purpose>-<nozzle>mm-nozzle-<layer>mm-layer`.
- **`<file-name>.md`** contains:
  1. Title (the profile name).
  2. Short description.
  3. When to use it, and when not to.
  4. Preset names and file locations.
  5. Default system presets it overrides (printer/process/filament) and which
     it changes.
  6. Target nozzle, the nozzle-dependent values it relies on (layer height,
     line widths), and how to adapt it to other nozzles.
  7. Table of changed parameters, grouped by preset: name (with Bambu Studio
     label), system value, final value, why.
  8. Models that use it.
- **Preset files:** `<file-name>.process.json` and/or `<file-name>.filament.json`
  (the main copies). Install identical copies as
  `~/Library/Application Support/BambuStudio/user/<user-id>/{process,filament}/<profile name>.json`,
  each with a `.info` file. Follow the format of the presets already there:
  - `.json`: only the changed keys plus `name`, `inherits` (system parent),
    `from: "User"`, `version` (from `system/BBL.json`), and
    `print_settings_id`/`filament_settings_id`. Restrict
    `compatible_printers` to the profile's nozzle.
  - `.info`: `sync_info = create`, `user_id`, empty `setting_id`, `base_id`
    (parent's `setting_id`), `updated_time`.
  - Quit Bambu Studio before writing. Never overwrite an existing preset.
- **Projects using a profile** select its presets directly, so Bambu Studio
  shows no unsaved changes. In `project_settings.config`:
  - `print_settings_id` / `filament_settings_id` = the preset names
  - `inherits_group` = the system parents (`""` for the printer)
  - every value exactly the preset's value
  - `different_settings_to_system` relative to the system parents
  Model-specific settings (e.g. supports) go per object, never project-wide.
  The project's printer preset and `nozzle_diameter` must match the profile.
- Reuse an existing profile by adding the model to its list. When a profile
  changes, update its `.md`, its preset files and the installed presets
  together.

## Rules for generated `.3mf` files

1. **Always a full Bambu Studio project, never a plain mesh 3MF.** A plain
   3MF, such as `trimesh.export` writes, loads as geometry only and Bambu
   Studio reports an invalid config. The archive needs:
   - `[Content_Types].xml`, `_rels/.rels`
   - `3D/3dmodel.model`: `Application` = `BambuStudio-<version>`, an object
     whose component points to `3D/Objects/object_1.model`, and the build item
   - `3D/_rels/3dmodel.model.rels`, `3D/Objects/object_1.model` (mesh)
   - `Metadata/` `project_settings.config`, `model_settings.config`,
     `slice_info.config`, `cut_information.xml`, `filament_sequence.json`
2. **Never hand-write `project_settings.config`.** Copy it from a project in
   this folder that uses the default setup (currently
   `third-party-models/skadis-clipper-holder.3mf`: newest version, one filament). Take
   `Application` from that file's `version`.
3. **Colour:** set `filament_colour`, `filament_multi_colour` and
   `extruder_colour` in the settings, not the mesh (e.g. `#C12E1F` LEGO red).
4. **Other settings:** set the key and add it to
   `different_settings_to_system[0]` (sorted, `;`-separated). Supports are
   tree, plate-only: `enable_support: "1"`, `support_on_build_plate_only: "1"`.
   If only some objects need a setting, set it per object in
   `model_settings.config` (`<metadata key=... value=.../>`), not project-wide:
   project-wide supports fill holes that face the plate.
5. Store the mesh centred on its bounding box. Use the build-item transform
   to place it at the bed centre, resting on the plate (z = half height).
6. **Separate parts and alternative versions** go on their own plates. Plates
   form a grid with `cols = ceil(sqrt(n))`, spaced 307.2 mm (1.2 × bed). For
   0-based plate i: `x += (i % cols) * 307.2`, `y -= (i // cols) * 307.2`.
   Each `<plate>` lists its objects and has a descriptive `plater_name`. Each
   object gets its own `3D/Objects/object_<id>.model`.
7. Never modify or overwrite existing model files unless asked.

## Design rules

- **No supports needed:**
  - Overhangs at most ~45° from vertical.
  - Only short bridges.
  - A flat base on the bed.
- **Exception, sideways round studs:**
  - Up to 6 mm from a vertical wall: OK, with a slightly rough underside.
  - 7 mm on a 30 mm stud triggers Bambu's "floating cantilever" warning:
    treat it as a failure and reduce the overhang.
- **For a clean finish,** print such features as separate pieces, flat with
  the detailed face up, glued onto short (≤2 mm) keyed pegs (e.g. a D shape).
  Check that each piece seats without overlap and that a rotated piece doesn't
  fit.
- **If the user prefers supports,** use plate-only tree supports (rule 4) and
  confirm in the G-code that none land inside the model.
- One watertight solid with consistent winding, fitting the 256 x 256 mm bed.
- Food or drink items (mugs, cups) are not food-safe as printed: say so and
  recommend a food-safe coating.

## Workflow

- Generate with Python (`manifold3d` for CSG, `trimesh` for checks) in a
  scratchpad virtualenv. Keep scripts and temp files out of this folder
  unless asked.
- Verify before handing over:
  1. **Mesh:** watertight, consistently wound, a single body; the saved 3MF
     reloads with the same volume.
  2. **Slice headless:** `result.json` must show
     `"error_string": "Success."` and an empty `warning_message`.

     ```bash
     /Applications/BambuStudio.app/Contents/MacOS/BambuStudio \
       --slice 0 --outputdir <out-dir> <model>.3mf
     ```

     Warnings about missing `machine_full`/`process_full` and
     `Invalid T command (T65535)` are normal noise in CLI mode.
- Report size, volume/capacity where relevant, filament weight and print time
  from the slice.
