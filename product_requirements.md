# Pocket Town Product Requirements

## Product promise

Students practice money decisions in a safe Indian neighborhood simulation. They explore a 2.5D town and open visual novel chapters by tapping buildings. The main story and financial learning happen through illustrated locations, expressive characters, dialogue, branching choices, immediate and later consequences, and retry. Each chapter gives a reason to visit the next building, while later chapters reuse earlier skills in new situations.

## Users and jobs

| User | Job to be done | Evidence of success |
| --- | --- | --- |
| Student | Understand a financial choice and recover from a poor one without real loss or shame. | Can explain the ledger change and apply the same concept in a later scene. |
| NGO facilitator | Start a classroom session on shared or personal devices and support students who are stuck. | Can issue a class code, see progress and concept support needs, and use discussion prompts without exposing private choices. |
| Content author | Add a story branch or translation without changing engine code. | Validated content pack builds and its scene graph remains reachable. |
| Tester | Reproduce a path and compare it with another path at the same money gate. | Deterministic log and seed reproduce the same balances, weights, and event eligibility. |

## Required player experience

1. Select language, an optional money context, and a fictional goal. No real financial details are requested.
2. Explore a pixelated 2.5D map with layered buildings, streets, and an animated avatar. Tap any building to transition into its full visual novel chapter with an illustrated background, expressive character portraits, dialogue, and branching decisions.
3. Make a consequential choice. Show rupees, time, access to money, and social implications before or after the choice as appropriate.
4. See a clear consequence card, updated ledger, and a short explanation. Let the player compare an alternate path without receiving duplicate money.
5. Advance after the level's money objective and behavioral gate are evaluated. A moderate unresolved habit may cause a later, personalized bottleneck; a severe eligible issue returns the player to repair it first.
6. Revisit the exact earlier scene, replace the decision, recompute later balances and events, and resume where interrupted.
7. Finish the seven-level story with a Money Wall showing what was learned, revised, and verified. Avoid a public leaderboard based on wealth.

## Seven-level acceptance map

| Level | Must teach through a decision | Money objective | Later proof |
| --- | --- | --- | --- |
| 1 The Grocery List | Distinguish earned money, allocated grocery money, spending, saving, needs and wants. | Complete groceries and save ₹100 of personal money. | New basket or fair cost tests comparison. |
| 2 The First Account | Form a repeatable budget; distinguish deposits from illustrative interest. | Earn ₹1,000 through default simulated tasks, then save a chosen share. A stipend context uses an equivalent income goal. | Level 3 tests the plan after an expense. |
| 3 The Surprise Week | Use accessible savings for an unexpected necessary expense. | Pay it and retain a ₹300 accessible buffer. | Level 5 tests liquidity again. |
| 4 Prices Move | Revise a goal when prices and purchasing power change. | Build ₹1,500 at the revised fictional price. | Levels 5 and 7 compare nominal and real outcomes. |
| 5 Lock or Keep Ready | Compare access, term, simple/compound illustrative interest, and risk. | Reach ₹2,500 total goal funds while retaining the buffer. | Later expense tests the split. |
| 6 The First Pay Slip | Budget from net receipts; distinguish gross/net, revenue/profit, GST on receipts, and payment authorization. | Reach ₹3,500 verified cumulative inflows and reconcile the ledger. | Finale tests a payment request and goal plan. |
| 7 The Future Fair | Compare investment paths, diversification and risk; verify suspicious offers. | Reach a ₹5,000 protected goal fund; volatile market gains do not count. | Final integrated choice explains tradeoffs. |

The amounts are default fictional classroom values and scale together for alternate contexts. The full building-by-building story is in [comprehensive_overview.md](comprehensive_overview.md).

## Functional requirements

- A level is a navigable pixelated 2.5D map with multiple buildings and at least one meaningful choice in each required building. Building entrances and labels remain usable despite visual overlap.
- Every building interaction uses the visual novel chapter contract in [visual_novel_design.md](visual_novel_design.md): setup, dialogue or investigation, financial choice, visual consequence, reflection, and next story lead. Revisited buildings and repair scenes use state-aware follow-up chapters.
- Map movement uses tap-to-move on authored walkable routes. A building tap opens its scene directly or after a brief optional walking transition; precise avatar positioning is never required to learn.
- Characters and props render in the correct depth order. Foreground structures may fade or split into layers to keep the avatar and entrance markers visible. Visual novel choices, ledger, and consequences stay readable in a flat interface.
- Every concept has a prerequisite, an active choice, a consequence, and a later transfer moment.
- Gate decisions, balance math, and weight updates are deterministic and inspectable in a debug build.
- At least five behavioral demerits persist across levels: overspending, cash concentration, risk mismanagement, saving inconsistency, and verification neglect. Context rules prevent penalizing necessary spending or an emergency buffer.
- The event system can choose different eligible bottlenecks for players with the same rupee total but different decision histories. An authored fallback covers every required event type.
- A repair loop must have a reachable solution, one mandatory bottleneck at a time, hints, and no repeat of the same resolved loop.
- Save and resume work on a midrange Android device while offline. Sync is optional for gameplay.
- The game can run in English; Telugu and Urdu content paths and right-to-left review are release requirements.
- The facilitator can see aggregate reach, transfer, repair, and safety indicators, with no public ranking.

## Safety and educational constraints

- All banks, companies, returns, wages, prices, and tax examples are fictional or clearly illustrative. No real investment product, ticker, recommendation, or brokerage link appears.
- Do not require an unsafe offer, volatile investment return, real job, account opening, skipped essential purchase, or real credentials to progress.
- Never request Aadhaar, family income, account numbers, real UPI PIN, or OTP.
- Explain that simulation outcomes are assumptions, not promises. Show unfavorable as well as favorable market paths.
- A student's demerit scores are private simulation state, not a diagnosis, wealth rating, or public score.

## Non-goals for the first release

Real banking integration, real-money rewards, market feeds, investment advice, a chat companion that invents arbitrary financial scenarios, and teacher access to students' detailed personal choices are outside scope.

## Open product decisions before content lock

- Set exact session length after testing the first three levels with students; the current three-to-five-minute building target is a hypothesis.
- Choose and validate the first release languages with NGO partners. English, Telugu, and Urdu are the planned paths.
- Confirm whether a facilitator dashboard needs a separate web app or an API-backed view in the mobile app. The data contract remains the same.
