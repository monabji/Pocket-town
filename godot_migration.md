# Pocket Town Migration to Godot

## Scope and status

Godot 4 with typed GDScript is the authoritative implementation target. This migration updates the entire documentation suite; it does not assert that a Godot project, export, or preview has already been completed. The earlier local React Native scaffold is superseded. Port its useful authored content and numerical fixtures into Godot, then verify the actual Godot game at each phase exit.

Keep the seven levels, financial ladder, English-only presentation, Indian setting, five private behavioral weights, money gates, source-choice repairs, and building-based visual novel experience. See [comprehensive_overview.md](comprehensive_overview.md) and [game_rules.md](game_rules.md).

## Port mapping

| Earlier implementation material | Godot destination | Required adaptation |
| --- | --- | --- |
| React Native app routing and component state | `Main.tscn`, SceneRouter and GameSession | Scene lifecycle, signals, input modes and compatible save restoration |
| SVG-rendered town and character | `Town.tscn`, TileMapLayer, Sprite2D/AnimatedSprite2D assets | Original pixel textures, foot anchors, Y-sort, real collision shapes and camera |
| Timer-driven keyboard movement | `CharacterBody2D` controller and Input Map | Physics-step movement, normalized diagonals, E entrance detection, touch release/focus handling |
| Component-based building chapters | `VisualNovel.tscn` and native Control containers | Staged interior, character expressions, financial object panel, keyboard focus and button signals |
| TypeScript ledger and gate engine | Typed GDScript `FinancialEngine` extending RefCounted | Integer-paise arithmetic, validated inputs, explicit rounding and pure replay |
| Authored levels and effects | Versioned JSON in `game/content/` and approved resources | Stable IDs, data-only effect registry, prerequisites, English text and schema validation |
| Local browser/device storage | SaveManager with FileAccess below `user://` | One versioned aggregate, validated generations, backup recovery and browser-specific tests |
| Unit and content fixtures | `game/tests/` GDScript fixtures and SceneTree runner | Preserve expected accounts, income exclusions, weights, gates and repair results |
| Optional online interfaces | HTTPRequest adapters and JSON contracts | Request IDs, active-branch version checks, timeout/fallback and no provider keys in the game |

Source JavaScript/TypeScript and framework components are reference material, not Godot runtime code. Do not wrap the old app in a web view and call that the migration. Do not publish a web/native export until its Godot preview passes.

## Migration order

### 1. Establish the Godot foundation

Follow [godot_setup.md](godot_setup.md). Create the project, settings, Input Map, source layout, shared scene/resource interfaces, and headless runner. Rebuild the starter home chapter and town with original assets. Preview WASD/arrows, collisions, E interaction, touch controls, chapter input, and save/resume in Phase 0.

### 2. Port the numerical contract before expanding maps

Use agreed golden fixtures rather than treating all previous code behavior as correct. Starting money must separate personal earnings from household allocation. Transfers cannot count as earnings, investment value cannot satisfy protected goals, and a replaced choice cannot duplicate task pay or interest. Keep the scenario seed stable and financial time separate from real time.

GDScript supports typed integer state; JSON is a boundary that requires whole-number, range, category, and version checks. Define classroom-safe bounds before arithmetic or conversion. Content effects dispatch through an approved GDScript registry; never execute script text from content or model output.

### 3. Port one complete level in each phase

Use Phases 1–7 in [phases.md](phases.md). Connect every required building to a complete visual novel chapter before claiming its level is playable. Preserve concept prerequisites, meaningful alternate choices, reflection, and reachable money goals. Record a real Godot preview for each phase, including all previously completed levels.

### 4. Prove branch repair in Godot

By Phase 3, demonstrate the same-money/different-history path and return to the exact earlier decision. A replacement forks the branch, recomputes later money/weights/events, commits its consequence beat, and resumes the paused chapter. Old branches remain comparison-only. Repeat interruption tests during the source repair and return transition.

### 5. Add optional services and classroom delivery

The client remains offline-capable. Use authored English events whenever an AI request is invalid, unavailable, or times out. Sync is a queued backup mechanism, not authority to rewrite the local financial rules. Native Android delivery and Godot facilitator scenes need shared-device, pause/resume, and privacy checks. Browser persistence and offline reload need separate evidence.

## Parity acceptance checklist

- Both agreed safe routes complete all seven levels and their money gates.
- Level 1 saves personal ₹100 after groceries and travel; entrusted money is separate.
- Level 2 earns ₹1,000 through legitimate simulated work; transfers and interest are excluded.
- Level 3 pays the necessary expense and keeps an accessible ₹300 buffer.
- Level 4 revises the price/plan and funds the ₹1,500 goal.
- Level 5 reaches ₹2,500 across the eligible funds and preserves access to the required buffer.
- Level 6 reconciles the ledger and verified cumulative ₹3,500 inflows.
- Level 7 protects ₹5,000 without counting volatile gains and completes the integrated decision.
- Eligible overspending, saving, liquidity and verification histories produce explainable source repairs. Untaught concepts and single ordinary mistakes do not cause mandatory remediation.
- Identical commands are idempotent; changed choices replace earlier evidence and preserve deterministic scenario assumptions.
- Keyboard, touch, direct building entry, dialogue input and collisions work in the running Godot build.
- Saved dialogue beats, consequences and repair checkpoints restore on the same compatible active branch.
- English text, rupee glyphs, responsive layouts and pixel art are verified on supported targets.

## Release and repository cleanup

Once corresponding Godot parity checks pass, remove or archive superseded framework source through a documented cleanup commit. Preserve authored fixtures and useful content history before removing runtime files. Build instructions, automation and previews must all refer to Godot. Repository code paths should match [architecture.md](architecture.md), and no phase is complete merely because a previous framework passed similar tests.

Full online service integration, facilitator readiness, platform exports and pilot findings each require their own evidence. The migration record distinguishes planned work, imported content, passing headless fixtures, and actually previewed game behavior.
