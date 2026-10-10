# Pocket Town Content Authoring

## Content model

The seven pixelated 2.5D maps and building interactions live in versioned JSON content packs loaded by the Godot 4.x GDScript client. A pack contains logical map geometry, layered sprite assets, building footprints and entrances, scenes, choices, ledger effects, concept tags, English text keys, illustrative financial assumptions, and authored adaptive-event fallbacks. The Godot renderer displays content; the deterministic [game engine](game_rules.md) validates and applies effects. No chapter contains executable code.

The project root is `game/project.godot`. Store shipped packs under `game/content/`, addressed in Godot as `res://content/`, and load them with `FileAccess` and `JSON`. Content IDs resolve through a validated local registry to approved textures, `PackedScene` files or resources. Never accept an arbitrary resource or script path from a chapter or remote event. Optional typed `Resource` wrappers may validate imported data, but no third-party plugin is needed and JSON remains the authoring contract.

## Required IDs and relationships

```text
contentPackId → levelId → buildingId → sceneId → choiceId
                                   ↘ consequenceSceneId
conceptId → prerequisiteConceptIds → teachingSceneIds → transferSceneIds
eventTemplateId → targetWeight → sourceSceneId → returnCheckpoint
```

IDs are stable across English copy revisions. Never derive logic from visible dialogue text. A content pack has a semantic version and migration map for renamed IDs. Each active save is pinned to the pack version used when it began.

## 2.5D map contract

Each level map declares `projection: "angled_overhead"`, logical map bounds, `groundTileWidth`, `groundTileDepth`, a player spawn, camera bounds, walkable nodes or cells, and placed assets. Keep positions in logical map units so layout remains stable across device sizes. Streets use a rectangular ground grid, while painted building fronts, roofs, and layer overlap create depth. The perspective and starting asset dimensions are defined in [design.md](design.md).

Each placed building specifies `buildingId`, logical position, `assetId`, `groundAnchor`, collision footprint, reachable `entranceNodeId`, screen-independent tap polygon, label key, and associated scene IDs. Asset metadata may provide separate base, roof, and foreground layers with depth anchors and an occlusion policy. Decorative height has no effect on movement unless an explicit elevated route is authored.

The content validator checks that the spawn can reach every required entrance, footprints do not block the only repair route, interaction polygons resolve overlapping buildings sensibly, and each asset and English label exists. A repair pin attaches to the source building's entrance, not an arbitrary sprite corner.

Translate this data into a `Node2D` world with `TileMapLayer` ground and a `CharacterBody2D` player. Registered building scenes contain sprites, collision shapes and an entrance `Area2D`; the area's building ID resolves the chapter when the player presses E. Use foot anchors and `y_sort_enabled` for depth, not sprite top edges. The same ID drives direct tap entry and accessible building-list entry. A locked chapter shows an authored prerequisite hint instead of executing an unrelated scene. Movement, doorway use and pause behavior are game systems, not scripts embedded in the map pack.

## Scene contract

Every building resolves to a visual novel chapter following [visual_novel_design.md](visual_novel_design.md). A chapter groups its setup, dialogue/investigation, choices, consequences, reflection, and exit lead. Declare `chapterId`, entry conditions, `backgroundAssetId`, character and expression IDs, dialogue beat IDs, scene objects, and state-aware follow-up or repair variants. Save the current beat so a learner can resume without repeating a money transaction.

Each scene specifies:

- `sceneId`, `levelId`, `buildingId`, and `kind` (`dialogue`, `choice`, `consequence`, `reflection`, `repair`, or `gate`).
- `prerequisiteConceptIds` and any prerequisite scene/flag. A prerequisite limits advanced dialogue; basic exploration remains possible.
- `teachesConceptIds` and `reusesConceptIds`. A concept is considered taught only after an active decision and consequence, not after reading a line.
- Ordered English dialogue beats as stable text keys and speaker IDs, with background, character portrait/expression, and optional animation/audio cues. Provide text alternatives for information carried by artwork.
- Two or more plausible choices for a meaningful decision; each has an explanation key, deterministic effect IDs, and next scene ID.
- An immediate consequence, at least one delayed consequence or transfer reference, and an accessible replay comparison.
- A validity predicate that ensures a path is available with the player's resources and context.

Example, abbreviated and illustrative:

```json
{
  "sceneId": "l1.kirana.unit_price",
  "levelId": "l1",
  "buildingId": "kirana",
  "kind": "choice",
  "prerequisiteConceptIds": ["earn_spend_save"],
  "teachesConceptIds": ["compare_total_cost"],
  "reusesConceptIds": ["needs_wants"],
  "speakerId": "shopkeeper",
  "dialogueKeys": ["l1.kirana.offer_intro", "l1.kirana.ask_quantity"],
  "choices": [
    {
      "choiceId": "compare_unit_price",
      "labelKey": "l1.kirana.compare",
      "effectIds": ["l1.buy_required_basket_at_checked_price", "l1.evidence.price_checked"],
      "nextSceneId": "l1.kirana.receipt"
    },
    {
      "choiceId": "take_bundle",
      "labelKey": "l1.kirana.bundle",
      "effectIds": ["l1.buy_bundle", "l1.evidence.price_ignored"],
      "nextSceneId": "l1.kirana.receipt"
    }
  ],
  "transferSceneIds": ["l3.refill.basket_check"],
  "fallbackEventTemplateIds": ["overspend.refill.medium"]
}
```

The effects referenced above are authored, validated data records. The scene JSON contains no executable GDScript, expressions to evaluate, or arbitrary resource paths. A trusted GDScript effect registry maps approved effect IDs to deterministic operations; unknown IDs reject the pack. Financial values are integer paise and all rates/prices are labelled fictional assumptions. Native visual novel controls emit `choice_selected(scene_id, choice_id)`; only the engine commits the resulting state.

## Level content inventory

| Level | Required buildings | Core decision and transfer |
| --- | --- | --- |
| 1 Grocery List | Home, kirana shop, vegetable stall, bus stop, home return. | Complete needs, compare total/unit price, save ₹100; a later basket exposes ignored cost. |
| 2 First Account | School project desk, bank branch, passbook kiosk, home desk, courtyard. | Earn ₹1,000 in default path, build a repeatable budget, separate deposit from interest; Level 3 tests the plan. |
| 3 Surprise Week | Repair lane, clinic/pharmacy variant, bank kiosk, home, learning center. | Cover an unexpected need and retain accessible buffer; Level 5 tests access again. |
| 4 Prices Move | Market lane, bus/metro kiosk, copy shop, school noticeboard, home. | Revise a goal after changed prices; Level 5 compares interest with purchasing power. |
| 5 Lock or Keep Ready | Bank savings desk, time window, repair lane return, home. | Compare illustrative interest, term, and liquidity; later expense tests the split. |
| 6 First Pay Slip | Future workplace booth, fair stall, receipt counter, UPI booth, home ledger. | Budget from net receipts and verify payment; finale reuses both. |
| 7 Future Fair | Future Lab, library, cyber café, bank/UPI kiosk, fairground. | Compare risk and diversification, recognize suspicious offers, integrate the whole ladder. |

The full narrative, characters, money goals, and building dialogue intentions are in [comprehensive_overview.md](comprehensive_overview.md). Do not shorten a level to a single quiz screen.

## Writing rules for visual novel scenes

1. Begin with a relatable need or goal. Give two or three credible choices, including a safe route to completion.
2. Show what changed now, what could happen later, and how the ledger proves it. Keep explanations short enough for a mobile screen.
3. Do not make a character humiliate the player. Ms Nandini asks what changed and offers a hint when a repair is difficult.
4. Needs and wants are contextual. Travel, data, care, and celebrations can have legitimate roles depending on the persona.
5. Use fictional banks, shares, prices, and rates. Never name a real investable product or portray a projection as a promise.
6. Do not require real documents, PINs, OTPs, employment, or financial disclosure to complete a scene.
7. Include at least one later transfer scene for each of the eight named concepts. The Level 7 fairground can be the later integrated transfer for concepts introduced earlier in that same level.

## English content and assets

All dialogue, UI copy, signs, captions, and optional narration use English only. Store complete English sentences under stable keys with placeholders for amounts, names, and dates; content keys separate writing from game logic. Review financial terms in scene context with educators. Do not concatenate sentence fragments in code. Rupee formatting, accessible English text, and optional English narration are specified in [localization_and_delivery.md](localization_and_delivery.md).

Visual assets are referenced by IDs and have a text equivalent. The town uses ground tiles for the angled overhead view, building sprites with consistent perspective, character movement frames, ground shadows, and optional foreground props. All assets share an authored ground anchor and visual scale. Buildings must remain recognizable without color alone. A content pack may include low-memory assets for shared Android devices. Palettes, pixel treatment, proportions, and staged interior compositions follow [design.md](design.md).

## Build-time validation

The content build fails when an ID is missing, a dialogue key lacks its English text, a choice has no reachable next scene, a required building has no consequential interaction, a gate has no safe path, or a repair has no source and return checkpoint. A graph traversal checks cycles and unreachable nodes. A concept dependency check prevents untaught concepts in required dialogue or event eligibility. A financial validator checks that choices and rates stay within approved scenario bounds.

Run these checks from the Godot project with a headless GDScript validation entry point before export. Also verify every registered `PackedScene` and asset imports, every required entrance is reachable with the actual collision shapes, and the same action opens the same chapter through keyboard, touch and the building list. Phase previews must run the integrated Godot game and check the newest level's content and all earlier completed levels.
