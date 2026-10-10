# Pocket Town Quality and Learning Plan

## Test strategy

Verify the rules, authored paths, adaptive events, classroom delivery, and actual transfer of learning. A polished map is insufficient if the money ledger is wrong or a learner can finish only by taking an unsafe shortcut.

The future implementation is Godot 4.x standard GDScript with the Compatibility renderer. Place fixtures and test runner scripts in `game/tests/`, scenes in `game/scenes/`, and shared data schemas in `contracts/`. Pin the editor and export templates in the build record.

| Test layer | Cases | Pass condition |
| --- | --- | --- |
| Engine unit tests | Paise math, deposits, transfers, interest, inflation example, weight deltas, thresholds, concept eligibility, gate results. | Exact expected state for fixed choice sequences; no floating-point drift or category confusion. |
| Engine properties | Replay, duplicate event IDs, branch recomputation, nonnegative constrained balances, same seed/choices. | No duplicate pay or interest; deterministic result; old branch read-only. |
| Content graph | All scenes and choices, prerequisites, English text keys, fallback events, safe money paths. | Every required scene reachable; every repair solvable; no untaught required concept. |
| 2.5D map | `Node2D`/`TileMapLayer`, foot anchors, `y_sort_enabled`, overlapping building taps, roof/prop occlusion, `Camera2D` limits, walkable routes and repair entrance pins. | Correct visual order; visible target selects its scene; every required entrance is reachable; labels remain readable at supported scales and text sizes. |
| Movement and focus | `CharacterBody2D` WASD/arrows, normalized diagonals, `move_and_slide()`, foot colliders, `Area2D` E prompt, touch actions, direct taps, focus loss, scene switches. | Equal directional speed; no wall crossing; all entrances usable; visual novel mode stops movement and clears held actions; keyboard and touch reach the same chapter. |
| Pixel design and scene layout | Nearest-neighbor asset sampling, consistent overhead perspective, character anchors, bottom dialogue panel, financial objects, portrait/landscape reflow and English text expansion. | Pixel world remains crisp; dialogue and amounts remain readable at native size; scene composition follows [design.md](design.md). |
| Visual novel chapters | Every building entry, backgrounds, expressions, dialogue beats, branching choices, financial objects, consequence, follow-up visits and interrupted chapter resume. | All buildings open complete narrative scenes; story and ledger match active decisions; resuming a committed consequence never applies it twice. |
| Bot personas | Careful planner, reckless spender, one-time saver, cash-heavy long-horizon learner, concentrated-risk learner, verification neglect. | Distinct eligible events at equal money totals, and every bot can finish all seven levels. |
| AI event tests | Out-of-bounds values, real stock tips, credential request, contradictory ledger, prompt injection, timeout, offline. | Invalid output rejected and authored fallback shown; engine state unchanged by the model. |
| Device and export tests | Installed Android APK and Windows export first; optional single-threaded Compatibility Web export over localhost/HTTPS; controls, save/resume, low memory, intermittent network. | No blocked required chapter; native saves survive close/reopen; browser persistence/caching limits are tested and communicated; performance budgets come from pilot devices. |
| English accessibility | Godot `Control` focus, explicit large-text setting, assistive reading support verified per platform, color-independent cues, English captions/audio, and terminology. | Meaning, amount, and choice effect stay equivalent across supported modes; every visible string is English; no unsupported screen-reader claim. |
| Classroom test | Shared device handoff, class code, offline lesson, facilitator discussion and aggregates. | Students' sessions remain separate; facilitator can support without exposing private choices. |

## Mandatory preview after every phase

Every future implementation phase in [phases.md](phases.md) ends with an actual running Godot game preview. A document, isolated module test, screenshot mockup, or unconnected map does not satisfy this gate. Use F6 to inspect changed scenes and F5 to run the integrated project, then preview relevant Windows/Android exports on the target device or emulator. Screen-share or record the running build.

Phase 0 previews the runnable starter map, home introduction, and resume. Phases 1–7 must demonstrate their newly added complete level, its concept decisions and money objective, and regression of every previously completed level. Show W/A/S/D and arrow movement, normalized diagonal speed, collisions, entrance proximity E, mobile directional controls, direct building taps, chapter focus, movement locking, full visual novel chapters, branching decisions, visible consequences, and accurate gates. Demonstrate offline play and interruption/resume once introduced; demonstrate bottleneck, source-choice repair, recomputation, and return to the paused chapter once those systems exist. Later previews repeat these checks across the growing curriculum. Include English readability, pixel depth, reachable entrances, and consistent rupee display.

Store evidence for each phase: phase ID, build/commit and content versions, device or emulator, demonstrated level and route, expected versus observed balances and gates, preview recording or review notes, defects, and resolution status. Mark a phase complete only after its implementation checks and observed preview pass. A failed preview returns the affected work to its owner; fixes require rerunning that path and relevant earlier-level regression.

Future evidence belongs in `previews/phase-XX.md` with media in `previews/phase-XX/`. A document revision does not constitute Godot preview evidence.

## Godot verification workflow

Use a plain GDScript `SceneTree` runner with assertions and a nonzero exit code for failed fixtures. Run `godot --headless --path game --script res://tests/run_tests.gd` after importing resources. Keep money/branch/content tests independent of visual nodes. Headless scene fixtures verify exported properties, collision/entrance placement, scene references, and input-mode state; they cannot establish pixel appearance, real touch behavior, or assistive technology support.

Run F6 scene checks, F5 integrated paths, and actual exports for visual/interaction gates. Inspect nearest-filtered world textures, feet-based sorting, `AnimatedSprite2D` facing/steps, `Control` layout/focus, `RichTextLabel` wrapping, large text, portrait/landscape, and a rupee-capable font. Stress focus loss and repeated confirm taps around committed transactions.

Optional Web release checks use standard GDScript, single-threaded Compatibility exports, complete export files, correct MIME types, and localhost/HTTPS hosting. Test initial load, PWA/service-worker caching, offline revisit after caching, cache eviction handling, and `user://` IndexedDB persistence after reopening; never promise first-visit offline use or durable private-browsing saves. Threaded exports require the appropriate cross-origin isolation headers. See the [Godot Web export guide](https://docs.godotengine.org/en/stable/tutorials/export/exporting_for_web.html).

## Required deterministic fixtures

1. **Level 1 pass with unresolved overspending:** ₹500 grocery allocation plus ₹100 personal task earnings; ₹400 required groceries, ₹50 travel, ₹50 poorly checked bundle; save personal ₹100; two independent poor comparisons yield weight 30 and later bottleneck.
2. **Level 2 earnings accounting:** task receipts reach ₹1,000; a deposit moves money but adds no earnings; illustrative interest is a separate entry and cannot satisfy the earning gate.
3. **Liquidity repair:** a player locks too much money in Level 5, then faces a necessary expense. Repairing the bank split preserves the accessible buffer and recalculates later interest and balances.
4. **Market safety:** positive market movement cannot satisfy the Level 7 protected fund; a negative movement is possible but does not create an impossible gate.
5. **Safety override:** a simulated credential-sharing choice triggers an immediate repair even if the verification weight is otherwise low.
6. **Interrupted chapter and repair branch:** close the app immediately after a committed purchase, resume its consequence without repeating payment, then repair the earlier choice and interrupt the branch switch. Restore the active branch's recomputed checkpoint and reject the old branch's resume record.

## Learning evaluation

Instrument concept exposure, active decision, explanation viewed, later transfer scene, hint use, repair, and completion. Avoid interpreting a high rupee balance as learning. Compare a concept's first active choice with a later changed-context choice: for example, unit-price comparison at the kirana shop versus a fair refill cost; a weekly budget versus an unexpected repair; interest shown in a passbook versus a time-bound deposit; or a single stock versus a diversified fictional basket under loss.

A small NGO pilot should measure completion, comprehension of why a consequence occurred, and improvement on transfer scenes. Record money context and comprehension difficulties so an apparently weak result can be investigated for wording or scenario mismatch. Do not claim the game improves real financial outcomes without a study designed to test that claim.

## Safety and content review

Before a content pack is accepted, review every financial illustration for age fit, fictional labeling, no product recommendation, accessible alternatives, and no real credential collection. Check that bank eligibility and tax examples are described as illustrative and that current rules are verified against primary guidance before public release. Review generated dialogue samples and authored fallbacks for clear, age-appropriate English.

## Release gates

- All seven money objectives have at least two safe feasible routes under each supported money context.
- Every mandatory bottleneck has a source decision, reachable repair, hint, return checkpoint, and no repeat loop after resolution.
- Installed Android/Windows offline play and `user://` save/resume work across a full playthrough; cloud sync is idempotent once implemented. Optional Web caching and persistence pass their separate, explicitly limited delivery gate.
- All seven 2.5D maps have correct depth, reachable entrances, reliable building hit areas, and readable markers on target devices.
- Every building opens a visual novel chapter with character dialogue and state-aware follow-up behavior; teaching chapters include consequential choices, visual outcomes, and financial explanations.
- The eight named concepts appear in an active choice and visible consequence, then in a later transfer moment.
- The AI cannot alter money, weights, gates, or authorized scene graph; invalid output falls back safely.
- Pilot users can explain at least the key consequences and use the map and visual novel without facilitator intervention for ordinary navigation.

Keep a release checklist with test run IDs, content version, device list, English content review sign-off, known issues, and the decision to ship. Thresholds for quantitative pilot improvement should be set before collecting pilot results.
