# Pocket Town Game Rules

This is the deterministic contract for the engine. Values below are starting tuning assumptions. Content authors can vary fictional prices and tasks inside validated bounds, but may not change the meaning of a gate or ledger category.

## Godot implementation contract

Implement these rules in a typed GDScript `FinancialEngine` extending `RefCounted`, under `game/scripts/engine/`. Keep it independent of the scene tree, frame timing, input events, animation, and networking. `GameSession` sends validated commands and receives a candidate state; the UI displays the result only after SaveManager commits the decision, snapshot, branch metadata, and consequence beat together. Movement through a `CharacterBody2D` does not change money or advance financial time.

Use GDScript 64-bit `int` for paise and integer rate/time inputs. Validate safe bounded values and intermediate products; use explicit integer quotient/remainder rounding rather than floating-point ledger math. Imported JSON numbers must be validated as whole values within the agreed range before conversion to `int`. For simple interest, multiply the bounded principal, rate in basis points, and authored days, then divide by `10000 * 365` using the agreed rounding rule. Compound periods apply the same explicit rounding rule at each specified period.

The financial scenario seed is stored in the save. Use a dedicated seeded `RandomNumberGenerator` only where a scenario requires it; world animation and decorative randomness must never consume the financial random sequence. Replaying a branch uses the same authored assumptions and scenario seed. Run engine and content fixtures through a headless Godot `SceneTree` test runner, including both safe full-game routes, repairs, and duplicate-command handling.

## Money and time

- Store every amount as integer paise. A displayed ₹100 is `10000` paise. Never use floating point for ledger arithmetic.
- Separate **personal spendable cash**, **money allocated for a named expense**, **accessible savings**, **time-bound deposit**, **fictional investment value**, and **protected goal fund**. A transfer moves value between accounts; it does not create earnings.
- Ledger entries have a source category: `task_earning`, `stipend_income`, `expense_allocation`, `transfer`, `deposit`, `withdrawal`, `interest`, `sale_revenue`, `cost`, `tax_example`, `market_change`, or `reversal`.
- Each scene advances a defined fictional time amount. Interest accrues only on eligible balances over that time. Use content-versioned, illustrative rates and explicit rounding. Display principal, contributions, and interest separately.
- An expense cannot make a balance negative unless a specific, safe fictional debt mechanic is deliberately added later. The first release has no borrowing gate.

## Level gates

| Level | Gate 1 money objective | Extra condition |
| --- | --- | --- |
| 1 | Save ₹100 from personal money after the grocery trip. | Complete the required grocery list and safe return; unspent entrusted grocery money does not count as personal savings. |
| 2 | Earn ₹1,000 from approved simulated tasks in the default path. | Choose a sustainable savings deposit. A stipend context uses an equivalent, accurately labelled income goal. Transfers and interest do not count as earnings. |
| 3 | End the week with a ₹300 accessible buffer after paying the necessary expense. | A locked deposit or fictional market value does not count as accessible. |
| 4 | Build a ₹1,500 goal fund using the updated fictional price. | Show the revised goal date or savings plan. |
| 5 | Hold ₹2,500 across accessible and goal funds. | Keep the Level 3 required buffer accessible after any deposit choice. |
| 6 | Reach ₹3,500 in verified cumulative simulated inflows. | Reconcile gross, deductions, net receipts, and expenses; exclude transfers, interest, borrowing, and scam proceeds from inflows. |
| 7 | Hold a ₹5,000 protected goal fund from planned additions and legitimate receipts. | Volatile investment gains do not count toward the gate; complete the final integrated decision. |

For alternate contexts, scale all fictional amounts and costs together and keep the same decision difficulty. A preflight validator must show at least two safe paths to each gate using the resources available in that level. Money alone never proves mastery.

## Behavioral weights

Each weight is an internal demerit from 0 to 100; higher means more unresolved evidence in this playthrough. The five weights are:

| Key | Evidence | First valid teaching point |
| --- | --- | --- |
| `overspending` | Repeated impulse buying beyond a plan or ignoring full/unit cost. Necessary spending is exempt. | Level 1 price comparison. |
| `saving_inconsistency` | Depending on one windfall rather than a realistic repeatable budget. | Level 2 budget. |
| `risk_mismanagement` | Failing to keep accessible money for a known near-term need, or later overexposing a goal to volatility. | Level 3 for liquidity; Level 7 for market risk. |
| `cash_concentration` | For a long-horizon goal, ignoring taught inflation effects; after investing is taught, concentrating investable money in one asset. Emergency and near-term cash are exempt. | Level 4 for purchasing power; Level 7 for asset concentration. |
| `verification_neglect` | Ignoring sender, terms, recipient, or authorization screen in a simulated offer or payment. | Level 6 payment scene. |

An authored rule links a choice to a concept, context predicate, evidence ID, and weight delta. The initial example is `+15` for an ignored unit-price check and `-10` for a later sound comparison. Clamp to `[0,100]`. These deltas and thresholds are tunable after playtesting; never present them as a judgment of a real student. Do not raise a weight for an untaught concept.

Recompute weights from the active decision branch and later improvement evidence. A replayed choice **replaces** the old evidence; it does not stack with it. A later good choice decays the matching demerit. Keep a debug explanation for every update, while the player sees only constructive feedback.

## Gate 2 decision

At a level boundary, inspect only weights whose prerequisite concepts have appeared in an active choice and whose evidence is sufficient. Use the highest applicable weight:

| Weight | Result |
| --- | --- |
| 0–29 | Advance normally. |
| 30–59 | Advance and schedule one relevant later bottleneck. |
| 60–100 | Route to a short source-scene repair before advancing. |

Ordinary mandatory repair requires evidence from more than one decision. A critical simulated credential-sharing choice may trigger an immediate safety repair regardless of score. If multiple weights qualify, select the one with the strongest evidence; break ties by oldest unresolved evidence, then a stable key order. Never queue more than one mandatory bottleneck at once.

The selector must use a scenario the player can recognize with concepts already taught. It cannot produce a market-loss lesson before Level 7 or penalize an emergency buffer for being cash. See [ai_event_engine.md](ai_event_engine.md).

## Repair, rewind, and fairness

1. An event stores the exact `sourceSceneId`, `sourceDecisionId`, `pausedCheckpoint`, target weight, and content version.
2. The repair pin returns to that scene. The player makes a replacement decision with the original bounded resources and may request a hint.
3. The engine creates a new branch from the source snapshot and recomputes all later ledger entries, time steps, weights, gates, and eligible events in order.
4. The old branch remains read-only for comparison. It cannot yield extra task pay, interest, or a second gate reward.
5. The resolved event ID is marked complete. The same event cannot trap the player again; a different later issue may still appear.

A more thoughtful path may end with less money. Feedback explains the tradeoff and does not simply label the higher balance correct. For comparison, a market scene uses the same fixed scenario seed for both branches.

## Worked Level 1 to Level 3 fixture

The player starts with ₹500 allocated for groceries and ₹100 earned from a supervised fair task. They buy required groceries for ₹400, pay ₹50 for travel, and take a ₹50 bundle without checking quantity; they also skip a vegetable price comparison. The grocery allocation is fully used, while the personal ₹100 remains saved, so Level 1 Gate 1 passes. Two independent poor comparisons raise `overspending` to 30. Gate 2 permits Level 2 and schedules a later event. Level 2 task earnings reach ₹1,000, but the unresolved weight remains. A Level 3 refill cost exposes the earlier basket decision. The repair returns to the kirana scene, replaces the choice, and recomputes the later path without paying the ₹100 again.

## Engine invariants

- Every ledger transaction balances and has a unique event ID.
- Wallet plus account transfers do not increase total money.
- Interest cannot be counted as task earnings; market gains cannot satisfy the Level 7 protected-fund gate.
- Every gate has a safe reachable path and every repair has a reachable valid answer.
- No player is penalized for a concept before its teaching scene.
- The same initial state, content version, scenario seed, and choice sequence yield the same state and gate result.
