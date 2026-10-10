# Pocket Town Quality and Learning Plan

## Test strategy

Verify the rules, authored paths, adaptive events, classroom delivery, and actual transfer of learning. A polished map is insufficient if the money ledger is wrong or a learner can finish only by taking an unsafe shortcut.

| Test layer | Cases | Pass condition |
| --- | --- | --- |
| Engine unit tests | Paise math, deposits, transfers, interest, inflation example, weight deltas, thresholds, concept eligibility, gate results. | Exact expected state for fixed choice sequences; no floating-point drift or category confusion. |
| Engine properties | Replay, duplicate event IDs, branch recomputation, nonnegative constrained balances, same seed/choices. | No duplicate pay or interest; deterministic result; old branch read-only. |
| Content graph | All scenes and choices, prerequisites, language keys, fallback events, safe money paths. | Every required scene reachable; every repair solvable; no untaught required concept. |
| 2.5D map | Sprite anchors, depth sorting, overlapping building taps, roof/prop occlusion, camera bounds, walkable routes and repair entrance pins. | Correct visual order; visible target selects its scene; every required entrance is reachable; labels remain readable at supported zoom and text sizes. |
| Pixel design and scene layout | Nearest-neighbor asset sampling, consistent overhead perspective, character anchors, bottom dialogue panel, financial objects, portrait/landscape reflow and translated text. | Pixel world remains crisp; dialogue and amounts remain readable at native size; scene composition follows [design.md](design.md). |
| Visual novel chapters | Every building entry, backgrounds, expressions, dialogue beats, branching choices, financial objects, consequence, follow-up visits and interrupted chapter resume. | All buildings open complete narrative scenes; story and ledger match active decisions; resuming a committed consequence never applies it twice. |
| Bot personas | Careful planner, reckless spender, one-time saver, cash-heavy long-horizon learner, concentrated-risk learner, verification neglect. | Distinct eligible events at equal money totals, and every bot can finish all seven levels. |
| AI event tests | Out-of-bounds values, real stock tips, credential request, contradictory ledger, prompt injection, timeout, offline. | Invalid output rejected and authored fallback shown; engine state unchanged by the model. |
| Device tests | Midrange Android first, then iOS; touch targets, map movement, save/resume, low memory, intermittent network. | No lost progress or blocked scene; acceptable load and response times set from pilot devices. |
| Accessibility and language | Large text, screen reader order, color-independent building cues, captions/audio, Telugu text, Urdu right-to-left. | Meaning, amount, and choice effect stay equivalent across supported languages and modes. |
| Classroom test | Shared device handoff, class code, offline lesson, facilitator discussion and aggregates. | Students' sessions remain separate; facilitator can support without exposing private choices. |

## Required deterministic fixtures

1. **Level 1 pass with unresolved overspending:** ₹500 grocery allocation plus ₹100 personal task earnings; ₹400 required groceries, ₹50 travel, ₹50 poorly checked bundle; save personal ₹100; two independent poor comparisons yield weight 30 and later bottleneck.
2. **Level 2 earnings accounting:** task receipts reach ₹1,000; a deposit moves money but adds no earnings; illustrative interest is a separate entry and cannot satisfy the earning gate.
3. **Liquidity repair:** a player locks too much money in Level 5, then faces a necessary expense. Repairing the bank split preserves the accessible buffer and recalculates later interest and balances.
4. **Market safety:** positive market movement cannot satisfy the Level 7 protected fund; a negative movement is possible but does not create an impossible gate.
5. **Safety override:** a simulated credential-sharing choice triggers an immediate repair even if the verification weight is otherwise low.
6. **Interrupted chapter and repair branch:** close the app immediately after a committed purchase, resume its consequence without repeating payment, then repair the earlier choice and interrupt the branch switch. Restore the active branch's recomputed checkpoint and reject the old branch's resume record.

## Learning evaluation

Instrument concept exposure, active decision, explanation viewed, later transfer scene, hint use, repair, and completion. Avoid interpreting a high rupee balance as learning. Compare a concept's first active choice with a later changed-context choice: for example, unit-price comparison at the kirana shop versus a fair refill cost; a weekly budget versus an unexpected repair; interest shown in a passbook versus a time-bound deposit; or a single stock versus a diversified fictional basket under loss.

A small NGO pilot should measure completion, comprehension of why a consequence occurred, and improvement on transfer scenes. Record language and money context so an apparently weak result can be investigated for translation or scenario mismatch. Do not claim the game improves real financial outcomes without a study designed to test that claim.

## Safety and content review

Before a content pack is accepted, review every financial illustration for age fit, fictional labeling, no product recommendation, accessible alternatives, and no real credential collection. Check that bank eligibility and tax examples are described as illustrative and that current rules are verified against primary guidance before public release. Review generated dialogue samples and fallbacks in each supported language.

## Release gates

- All seven money objectives have at least two safe feasible routes under each supported money context.
- Every mandatory bottleneck has a source decision, reachable repair, hint, return checkpoint, and no repeat loop after resolution.
- Offline play and save/resume work across a full playthrough; cloud sync is idempotent.
- All seven 2.5D maps have correct depth, reachable entrances, reliable building hit areas, and readable markers on target devices.
- Every building opens a visual novel chapter with character dialogue and state-aware follow-up behavior; teaching chapters include consequential choices, visual outcomes, and financial explanations.
- The eight named concepts appear in an active choice and visible consequence, then in a later transfer moment.
- The AI cannot alter money, weights, gates, or authorized scene graph; invalid output falls back safely.
- Pilot users can explain at least the key consequences and use the map and visual novel without facilitator intervention for ordinary navigation.

Keep a release checklist with test run IDs, content version, device list, language review sign-off, known issues, and the decision to ship. Thresholds for quantitative pilot improvement should be set before collecting pilot results.
