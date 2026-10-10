# Pocket Town Visual Novel Design

Pocket Town's main teaching experience is a visual novel. The 2.5D town connects locations and makes the learner curious about where to go. Tapping every building opens an illustrated chapter where characters introduce a money problem, the player chooses, and the story shows what changed. This applies to the home, shops, bank, repair lane, kiosks, learning center, and fairground throughout all seven levels.

## Building entry and screen composition

1. Tap a building or select it from the accessible building list.
2. Resolve the chapter appropriate to the current level, prerequisites, earlier choices, and active repair. A brief entrance transition can be skipped.
3. Open a dedicated visual novel screen with an illustrated interior or location background, character portraits or sprites, expressions, a speaker name, and a dialogue box.
4. Present story beats at the player's pace. At decision points, show two or three plausible choices and any known cost, time, or condition needed to understand them.
5. Show the chosen outcome in the scene, then explain the exact ledger change with a readable financial object or overlay. End with reflection and a lead to another building or follow-up chapter.

The visual novel screen follows [design.md](design.md): a pixel interior with the player staged in the foreground left, an NPC near a counter or desk farther right, and a prominent bottom dialogue panel inspired by the final supplied image. Use larger staged sprites for expressions and gestures, with optional portraits when helpful. The screen keeps character and story context visible while the player chooses. Text and choices have sufficient contrast and scale independently of the artwork. Financial receipts, passbooks, payslips, charts, and goal boards are scene objects that can open a readable overlay. After inspecting them, the learner returns to the same dialogue beat.

## Chapter structure

| Beat | Narrative purpose | Financial learning |
| --- | --- | --- |
| Arrival | Establish the location, people, and immediate need. | Explain what money or resources are available and who owns them. |
| Conversation or investigation | Let the player ask questions, inspect an object, or compare options. | Expose the price, terms, time, access, or uncertainty that matters. |
| Choice | Ask the player to act with the information available. | Apply the level's concept and an earlier skill. |
| Visual consequence | Change a character reaction, basket, receipt, passbook, calendar, or other scene element. | Show the direct outcome before explaining it. |
| Explanation and reflection | Let the character connect the result to the decision and ask what could change. | Make the calculation and tradeoff clear. |
| Next lead | Give a story reason to return home, visit the bank, compare another shop, or continue later. | Carry the consequence and concept into the next chapter. |

Use several short beats rather than one large block of text. A chapter can contain multiple decision points where they support one coherent situation. Meaningful decisions change money, resources, time, access, evidence, or the later story. Character expressions can reinforce consequences, while the numbers and feedback remain understandable without artwork or audio.

## Example Level 1 linked chapters

**Home:** Farah gives the player the grocery list and distinguishes household grocery money from the player's school-task earnings. Dialogue establishes the meal and a safe return trip. The player marks essential items, an optional item, and a spending limit. Farah sends them to the kirana shop with a reason to compare quantity and full cost.

**Kirana shop:** The shopkeeper displays a bundle beside the usual item. The learner can ask about quantity, inspect unit price, or accept the bundle. The basket and receipt change after the choice. Dialogue shows which groceries and return-trip money remain; the ledger uses the exact authored amounts.

**Vegetable stall:** A different character offers produce in different quantities. The player reuses the comparison skill in a new purchase. The scene shows the resulting basket and directs the player home.

**Home return:** Farah reacts to what the basket can actually provide. Ms Nandini asks what the plan included or missed. The player compares the old decision with a revised one and sees why ₹100 saved is only part of the result. A story lead introduces the bank and the next level's budget.

These chapters respond to prior choices. A missing ingredient changes the home conversation. A sound comparison changes the follow-up explanation. The player visits locations because the evolving story creates a need.

## Examples across the ladder

| Building or location | Visual novel treatment |
| --- | --- |
| Bank branch, Level 2 | Staff dialogue explains account purpose; the player asks questions and inspects a fictional passbook. A deposit and an illustrative interest entry remain distinct in the story and ledger. |
| Repair lane, Level 3 | A mechanic explains two quotes. The player inspects terms, chooses a repair, and sees the revised budget and accessible buffer through dialogue and a bill. |
| Market and noticeboard, Level 4 | Characters compare an old receipt with a new price, then discuss the changed goal date. The board updates after the player's revised plan. |
| Bank desk and time window, Level 5 | Teja and a staff character compare accessible savings and a fictional deposit. An interactive timeline supports their conversation about interest, inflation, and access. |
| Workplace and UPI booths, Level 6 | A future-life chapter presents a payslip and a payment screen. The learner distinguishes net receipts and authorization through story choices. |
| Library, cyber café and fairground, Level 7 | Teja compares fictional market paths; Aditi brings a suspicious message; the final chapter combines goal protection, risk, diversification, and verification. |

## Adaptive and repair chapters

A later bottleneck begins in a visual novel chapter and refers to an earlier active decision in plain language. The character reveals the new situation, a financial object shows the cost or access problem, and the player can follow a repair lead to the earlier building. The source chapter loads a repair variant that highlights the relevant decision without replaying unrelated dialogue. After the replacement choice, the engine recomputes the branch and the paused later chapter resumes with dialogue and financial objects matching the revised state.

The AI can personalize approved character lines and event variants inside this chapter structure. It cannot change balances, generate new choice effects, or invent a new financial claim. Authored chapters provide the same complete experience offline. See [ai_event_engine.md](ai_event_engine.md).

## Chapter content and save contract

Every building chapter declares a chapter ID, entry conditions, background asset, character IDs and expression assets, ordered dialogue beats, stable English text keys, scene objects, choices, consequence branches, reflection, and exit leads. Alternative text covers essential visual information. Optional English narration references the same English dialogue beats; captions preserve their full meaning.

Save the playthrough, active branch, snapshot, chapter, scene, current beat, committed decision event, any open object overlay, and content version. Commit a decision, its resulting snapshot, and the post-choice resume beat in one local transaction. Restoring a chapter must match the active branch and display a previously committed consequence without applying its transaction again. A repair replaces the active resume record with the recomputed later checkpoint; it cannot restore dialogue from the old comparison branch. Revisits resolve to a follow-up, reflection, or repair chapter rather than automatically replaying the first visit.

## Acceptance criteria

- Every building opens the visual novel presentation and has a story reason for its interaction.
- Required teaching chapters include a meaningful choice, a visible consequence, a clear financial explanation, and a later lead or transfer.
- Returning to a building reflects prior decisions and the current story state.
- Repair chapters return to the exact earlier decision and resume the later narrative correctly.
- Learners can pause, revisit dialogue and explanations, skip text animation or optional transitions, enlarge text, and use captions or screen-reader alternatives.
- The map, staged character sprites, optional portraits, interior backgrounds, and financial objects follow the pixel art and dialogue composition defined in [design.md](design.md).
