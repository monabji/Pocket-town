# Pocket Town Project Documents

Pocket Town is a mobile financial decision game for students in Grades 11 and 12 in India. The player explores seven pixelated 2.5D neighborhood maps. Every building opens a visual novel chapter with illustrated locations, character dialogue, branching decisions, and visible financial consequences. These chapters carry the main learning experience and link visits across the town. All money and products in the game are simulated.

The game uses English throughout its interface, dialogue, signage, captions, and optional narration. Phases 1–7 each deliver the next complete level; every phase requires an actual runnable game preview and recorded verification before work advances.

## Read in this order

| File | What it answers |
| --- | --- |
| [comprehensive_overview.md](comprehensive_overview.md) | What is the complete game, story, seven maps, money ladder, buildings, and learning loop? |
| [design.md](design.md) | How should the pixelated 2.5D town and building visual novels look, using the supplied visual references? |
| [product_requirements.md](product_requirements.md) | What must the player, facilitator, and demo be able to do? |
| [game_rules.md](game_rules.md) | How do money, behavioral weights, gates, replay, and repairs work exactly? |
| [content_authoring.md](content_authoring.md) | How are maps and English branching scenes written, validated, and versioned? |
| [visual_novel_design.md](visual_novel_design.md) | How does every building deliver a visual novel chapter and teach through narrative choices? |
| [ui_flow.md](ui_flow.md) | What screens and interactions must the player experience? |
| [ai_event_engine.md](ai_event_engine.md) | How are player-specific bottlenecks selected and safely personalized? |
| [architecture.md](architecture.md) | What runs on the device and server, and how do the pieces communicate? |
| [data_model.md](data_model.md) | What data is stored, synced, and shown to facilitators? |
| [api_contract.md](api_contract.md) | What messages and error cases connect the app and server? |
| [localization_and_delivery.md](localization_and_delivery.md) | How do English content, accessibility, devices, and NGO classrooms work together? |
| [quality_and_learning.md](quality_and_learning.md) | What must be tested before claiming the game works or teaches? |
| [phases.md](phases.md) | How does each phase add a playable level, use subagents, and finish with a verified game preview? |

## Source of truth

- The overview owns story, educational scope, and level intent.
- `design.md` owns visual direction, the angled overhead perspective, pixel assets, scene staging, and interface treatment. Its four source images are preserved in `design_references/` within this project directory.
- `game_rules.md` owns deterministic arithmetic and progression behavior.
- `content_authoring.md` owns the scene format and content constraints.
- `ai_event_engine.md` owns adaptive event selection and generation boundaries.
- `architecture.md` and `data_model.md` own implementation interfaces and storage.
- If these disagree, record the decision in the relevant file and update links and examples in the others before implementation.

## First playable milestone

Phase 1 delivers all of Level 1; Phase 2 adds all of Level 2; Phase 3 adds all of Level 3 and completes the first cross-level repair milestone. A player can save ₹100, enter Level 2, earn ₹1,000 in the default simulated task path, encounter a later consequence driven by Level 1 overspending evidence, return to the earlier kirana decision, and resume Level 3 with recalculated balances. The same path must run offline from authored content, and each phase has its own runnable preview.
