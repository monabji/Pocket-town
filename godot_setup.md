# Pocket Town Godot Setup

This is the target setup for the Godot implementation. The paths and commands below become runnable when Phase 0 creates the project and test scripts; this guide does not assert that those files already exist.

## Engine and delivery targets

- Use the standard stable Godot 4 editor with typed GDScript. Record the exact engine version in the build report and install matching export templates. Obtain the editor and templates through [Godot's official downloads](https://godotengine.org/download/windows/).
- Use the Compatibility renderer for the layered 2D pixel world and browser delivery. Godot web export uses WebAssembly and WebGL 2.0; the standard GDScript project supports that target. See [Godot web export requirements](https://docs.godotengine.org/en/stable/tutorials/export/exporting_for_web.html).
- Primary targets are Windows for development/hackathon play and Android for NGO delivery. Provide a web export for browser previews when validated on the actual browser/device. Other platforms need their own export and verification before being described as supported.
- The Godot game owns the town, financial engine, visual novel, journal, local saves, and facilitator interface. Optional online services follow [api_contract.md](api_contract.md); offline completion cannot depend on them.

## Project layout

All documents and the Godot source stay within this repository. `game/` is the Godot project root, so `res://` refers to `game/`, not the repository root.

```text
game/
  project.godot
  export_presets.cfg
  scenes/
    main/Main.tscn
    world/Town.tscn
    player/Player.tscn
    buildings/Building.tscn
    visual_novel/VisualNovel.tscn
    ui/HUD.tscn
    ui/Journal.tscn
    ui/Facilitator.tscn
  scripts/
    autoload/
    engine/
    content/
    world/
    visual_novel/
    ui/
    network/
  resources/
    themes/
    maps/
    characters/
  content/
    levels/
    english/
    events/
  assets/
    tiles/
    buildings/
    characters/
    interiors/
    audio/
  tests/
    run_tests.gd
    fixtures/
contracts/
  content/
  saves/
  api/
services/                 Optional server implementation
previews/                 Real build evidence after each phase
exports/                  Generated delivery builds; ignored in Git
```

`design_references/` remains the original inspiration folder. Produce original game assets; the reference screenshots are not game textures. See [design.md](design.md).

## Phase 0 editor configuration

1. Create the project under `game/`, choose Compatibility, and save the project settings in `project.godot`.
2. Create `Main.tscn` and set it as the main scene. Use F5 to test the complete game; F6 is useful for an isolated scene but does not prove integration.
3. Establish a 480 × 320 logical world-art viewport, with a 960 × 640 initial desktop window, discrete world scaling and nearest texture filtering. Use a world `SubViewport` if needed to separate art scaling from dialogue and HUD `Control` elements at readable display size. Validate portrait layouts rather than stretching the whole landscape composition into a narrow screen.
4. Add the Input Map actions below. Use physical key bindings for movement so WASD positioning is predictable. Define touch equivalents with the same actions.
5. Register the agreed autoloads and their scripts. Keep the pure financial engine outside autoload nodes so fixtures can instantiate it without world/UI dependencies.
6. Configure export presets for Windows, Android, and Web. Keep platform credentials and signing keys outside tracked settings. Import assets before running headless checks or exports.
7. Record engine version, renderer, target device, input bindings, content version, and export-template version in the first preview report.

## Input Map contract

| Action | Keyboard | Touch/UI equivalent | Active mode |
| --- | --- | --- | --- |
| `move_up` | W, Up arrow | Up pad | Town |
| `move_down` | S, Down arrow | Down pad | Town |
| `move_left` | A, Left arrow | Left pad | Town |
| `move_right` | D, Right arrow | Right pad | Town |
| `interact` | E | Enter nearby building button | Town, valid entrance nearby |
| `dialogue_next` | Enter, Space | Continue button | Dialogue or consequence |
| `choice_1`, `choice_2`, `choice_3` | 1, 2, 3 | Labelled choice buttons | Choice stage only |
| `back` | Escape | Return/close button | Chapter or overlay |
| `journal` | J | Journal button | Allowed navigation state |

Handle UI focus separately from world input. A focused button and a dialogue shortcut must not process the same key press twice. Ignore key echo for decisions, consume handled events, and route choices through one command function. Native controller UI navigation may use Godot's `ui_*` actions alongside these custom actions.

`Player.tscn` uses `CharacterBody2D`, an `AnimatedSprite2D`, and a small foot-level `CollisionShape2D`. Use `Input.get_vector("move_left", "move_right", "move_up", "move_down")`, set velocity, and call `move_and_slide()` in `_physics_process()`. This follows [Godot's 2D movement pattern](https://docs.godotengine.org/en/stable/tutorials/2d/2d_movement.html). Financial time advances through authored choices, never through frame time or walking.

- Normalize diagonal movement; do not make diagonal travel faster.
- Buildings have a solid footprint and a separate `Area2D` entrance/interaction zone. Choose the nearest eligible entrance deterministically if zones overlap.
- A direct building tap or accessible building list opens the same chapter. Precision positioning is not a prerequisite for learning.
- Optional ground tap navigation uses the authored walkable graph or `AStarGrid2D` and collision-aware movement; cancel the path when manual movement starts.
- Disable world input and set velocity to zero while reading a chapter or opening a modal. Clear touch holds and path targets on pause/focus loss. Require a fresh movement input on return so the avatar does not keep walking unexpectedly.
- Touch controls support held movement and simultaneous directions. A press/release pair must release its action even when the finger leaves the button.

## Scene responsibilities

| Scene/system | Responsibility |
| --- | --- |
| `Main.tscn` | Boot, compatible save restore, screen routing and error recovery |
| `Town.tscn` | Ground `TileMapLayer`s, Y-sorted buildings/characters, camera, labels and repair pins |
| `Player.tscn` | World-only movement, collision and facing/walk animation |
| `Building.tscn` | Asset anchor, footprint, entrance detection, tap target and stable chapter ID |
| `VisualNovel.tscn` | Interior stage, sprites/expressions, dialogue, objects, choices, reflection and saved beat |
| `HUD.tscn` | Level, goal, next lead and available funds |
| `Journal.tscn` | Active ledger, account distinctions, learning trail and branch comparison |
| `Facilitator.tscn` | Class setup and aggregate progress; no public demerit scores |

The visual novel scene uses `Control` containers, `TextureRect`/character stage elements, `RichTextLabel`, and `Button` nodes under a UI `CanvasLayer`. Opening a building routes to this presentation, not a floating quiz over the town. `choice_selected(scene_id, choice_id)` goes to GameSession; only the financial engine can compute the resulting money and gates. Follow [visual_novel_design.md](visual_novel_design.md).

Use `TileMapLayer` for separate ground and decoration layers, with intentional foot anchors and Y-sorted actor/building nodes. Godot's per-layer tile facilities are described in [TileMapLayer documentation](https://docs.godotengine.org/en/stable/classes/class_tilemaplayer.html). Tall roofs/canopies need split layers or an explicit occlusion policy; essential labels stay on the UI layer.

## Autoload and save setup

- **GameSession:** active playthrough, command orchestration, engine results and signals.
- **ContentRepository:** validated versioned JSON, stable English keys and approved resource registry.
- **SaveManager:** local versioned save aggregate and recovery; no real financial identity.
- **SceneRouter:** town/chapter/journal/facilitator transitions and input mode.
- **AudioManager:** optional English narration, ambience and user audio settings.
- **SyncClient / AdaptiveEventClient:** optional queued HTTPS requests and authored fallback.

The save aggregate contains decisions, active/archived branch metadata, snapshot, content version, scenario seed, and the branch-scoped visual novel beat. Store it below `user://`, using `FileAccess` and checked file operations. Write a new generation to a temporary file, close it, validate its contents, and promote it without destroying the previous valid generation. Restore the newest compatible validated generation; keep a verified backup. Do not claim filesystem writes provide database transaction semantics. See [Godot's save-file guide](https://docs.godotengine.org/en/stable/tutorials/io/saving_games.html) and [data_model.md](data_model.md).

For web exports, browser storage persistence and eviction must be tested separately. Private browsing, cleared site data, or denied persistence may remove a save; show a clear warning when persistence fails. A first browser visit still requires downloading the game. Offline browser reload is only supported after an explicitly configured and verified cache/PWA flow; native installed builds bundle their required content.

## Run, test and export

Add the editor executable to PATH as `godot`, or replace it with its absolute path. Run these commands from the repository root once Phase 0 provides the project and runner.

```powershell
godot --version
godot --headless --path game --editor --quit
godot --path game
godot --headless --path game --script res://tests/run_tests.gd
godot --headless --path game --export-release "Windows" ../exports/windows/PocketTown.exe
godot --headless --path game --export-release "Android" ../exports/android/PocketTown.apk
godot --headless --path game --export-release "Web" ../exports/web/index.html
```

Create export directories first and name presets exactly as used in commands. The test runner extends `SceneTree`, exits nonzero on failure, and checks deterministic engine/content fixtures. Headless checks verify rules and imports; visual/movement checks need the actual game. Android export also needs its configured platform SDK and signing settings.

Serve the web export through an HTTP server on localhost or HTTPS, preserving all generated filenames. Prefer the single-thread web preset initially. If a later build enables threads, configure and verify the required cross-origin isolation headers; do not silently assume the same hosting setup works. Confirm requirements against [Godot's web export guide](https://docs.godotengine.org/en/stable/tutorials/export/exporting_for_web.html).

## Source control

Track `.gd`, `.tscn`, `.tres`, source assets, content JSON, contracts, `project.godot`, export presets without secrets, and generated source `.uid` files. Ignore `.godot/`, temporary saves, local credentials, signing material and exported builds. Use one owner per shared scene/resource/settings file during subagent work. Each phase ends with the actual game preview in [phases.md](phases.md).
