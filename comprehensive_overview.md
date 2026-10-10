# Pocket Town Comprehensive Overview

**Purpose:** End-to-end game design for a financial decision RPG for Grades 11 and 12 in India. Each level is a pixelated 2.5D map, and tapping any building opens a visual novel chapter where the main story, decisions, and financial learning happen.

**Implementation documents:** [Visual design](design.md) · [Visual novel chapters](visual_novel_design.md) · [Architecture](architecture.md) · [Delivery phases](phases.md) · [Game rules](game_rules.md) · [Content authoring](content_authoring.md) · [Screen flow](ui_flow.md) · [Adaptive event engine](ai_event_engine.md) · [Data model](data_model.md) · [API contract](api_contract.md) · [Quality and learning evaluation](quality_and_learning.md) · [English accessibility and NGO delivery](localization_and_delivery.md) · [Project index](README.md)

*A financial decision RPG for Grades 11 and 12 in India*

Pocket Town combines a pixelated 2.5D town with a visual novel that teaches finance through story decisions. Each level is a map with buildings, streets, and a player avatar presented through layered sprites that create depth. Tapping a building transitions into a full visual novel chapter with an illustrated location, character portraits and expressions, dialogue, branching choices, visual consequences, and replay. The player moves between buildings to continue these linked chapters. The financial ladder begins with earning, spending, and saving, then reuses them while adding budgeting, inflation, interest, diversification, and risk. Rupee goals show what the player achieved; five behavioral demerits track how they achieved it. Earlier choices shape later events.

Static lessons and fixed scripts can teach definitions without testing whether a learner adapts when prices, cash needs, or risks change. This document defines every map, the two-gate progression system, bounded AI events, and tests for learning and safety. All rupee amounts are fictional classroom assumptions. The game gives no real product or investment recommendation.

## Game at a glance

| **Element** | **Design** |
| --- | --- |
| Audience | Grades 11 and 12, with English content and adaptable fictional money contexts suited to NGO delivery in India |
| Format | Godot 4.x Standard with GDScript and the Compatibility renderer; pixelated 2.5D maps made from layered 2D sprites; WASD/arrows to explore, E at a doorway or a direct building tap to enter a visual novel |
| Main learning experience | Every building opens a visual novel chapter with a location background, expressive characters, dialogue, consequential choices, and visible financial outcomes |
| Story goal | Prepare a personal next step goal and help friends build a Money Wall for the neighborhood fair |
| Learning ladder | Each new concept requires an earlier skill, is used in a building choice, and is tested again in a different later situation |
| Progression | Gate 1 checks the rupee target; Gate 2 checks five carried behavioral demerits for a clear pass, later bottleneck, or repair |
| Signature mechanic | Player-specific weights and assets trigger distinct events; a severe gap sends the player to an earlier decision to repair it |
| Session design | Three to five minutes per building; one level in roughly 15 to 25 minutes |

## Map presentation

Each of the seven levels uses a fixed angled overhead view with visible building fronts and roofs, connected streets, and clear entrance markers. Layered ground, shadows, buildings, characters, and foreground props give the town depth. Tap a building to open its current visual novel; a short avatar walk may support the transition but never becomes a barrier to the learning scene. Tap-to-move exploration uses authored walkable routes. Building labels and repair pins remain readable when the avatar passes behind a structure. Camera pan and optional zoom help explore the map, while the visual novel, choices, and ledger use a flat, readable interface. The pixel art, character staging, palette, and dialogue panels follow [design.md](design.md), based on the supplied visual references.

The executable client is the Godot project at `game/project.godot`. The town is a `Node2D` scene with `TileMapLayer` ground, a `CharacterBody2D` player, `AnimatedSprite2D` movement, building collision shapes and entrance `Area2D` nodes, and a bounded `Camera2D`. Normalize diagonal movement and sort world sprites by their ground anchors using `y_sort_enabled`; roof height never changes collision position. WASD and arrows move, E interacts with a nearby doorway, and direct taps or the building list provide the same chapter access without requiring precise walking. Space/Enter advances dialogue, 1/2/3 select choices, Esc closes the current overlay or returns safely, and J opens the journal. Touch controls use the same `InputMap` actions. Player movement pauses whenever the visual novel or a modal has input focus.

## Visual novel learning experience

The building interaction is a complete narrative scene: an illustrated interior or location background, characters with expression changes, a dialogue box with speaker name, and branching choices at meaningful points. A chapter begins with a relatable problem, lets the player ask or compare before deciding, shows the outcome through the characters and setting, and explains the financial change using a receipt, passbook, payslip, goal board, or compact ledger. Reflection and replay happen inside this story flow before the player returns to the map with a reason to visit another building.

Every building uses this presentation, including banks, shops, home, kiosks, and the learning center. Calculators, receipts, and charts appear as objects inside the scene or readable overlays, and their results feed the dialogue. Returning to a building loads a follow-up chapter that remembers earlier decisions. A personalized bottleneck also unfolds as a visual novel, then leads back to the earlier chapter for a revised choice. The detailed chapter and screen contract is in [visual_novel_design.md](visual_novel_design.md).

## Story and cast

The player is a final year school student in Gulmohar Ward, a fictional part of Hyderabad. The neighborhood learning center asks a student group to build a Money Wall for its Next Step Fair. Each student chooses a different goal: an application, regular travel, a device repair, or a course fee. Their circumstances vary, but their stories intersect at the kirana shop, bank, school, repair lane, and market. A suspicious offer begins circulating shortly before the fair. The player learns enough to judge it without being told that every ambitious goal is dangerous.

| **Character** | **Role in the story** |
| --- | --- |
| The player | Chooses a name, money context, and goal; all content is in English and no real financial data is requested |
| Farah | Friend saving for dependable travel; challenges the player to separate a useful expense from an impulse |
| Teja | Friend helping with the fair; curious about banks, interest, and later investing |
| Aditi | Friend who receives a suspicious scholarship or earnings message; the player helps verify it |
| Ms Nandini | Learning center facilitator; asks questions and helps players compare choices without giving answers |

The default story money context is a small monthly allowance plus school approved project payments. Alternate contexts replace the source with a stipend, family allocated money, or supervised fair earnings. The same concepts and difficulty are preserved; the game does not imply that every student has a job, bank account, or identical family support.

## The player loop

| **Step** | **What happens** |
| --- | --- |
| Explore | Move between buildings in any sensible order; certain dialogue appears only after a relevant earlier encounter. |
| Decide | Use an earlier money skill plus the level's new concept to choose among plausible options with different time, money, liquidity, risk, and social effects. |
| See consequences | A short scene shows today, next week, and a later outcome. The ledger explains the numerical change. |
| Reflect | The player compares paths, hears a plain language explanation, and may replay without extra money rewards. |
| Check gates | Gate 1 checks money. Gate 2 reads carried demerits and selects advance, advance with a later bottleneck, or immediate repair. |
| Repair | A personalized event points back to the exact earlier building where the player can improve a decision. |

## Progression and money rules

The money gates are story objectives, not tests of whether a child is wealthy. The numbers below are default classroom values and can be scaled together for a chosen money context. Each level grants a bounded, transparent set of fictional inflows and expenses so a player can reach the objective through several reasonable paths. A player never needs to choose an unsafe offer, gamble on a stock, skip a basic need, or depend on unusually high interest to progress.

### The financial concept ladder

A concept is learned only after the player uses it to make a decision, sees the consequence, and applies it again in a changed situation. The order below controls dialogue prerequisites and when a behavioral weight may count toward Gate 2. Levels can be explored freely within their maps, but advanced choices never assume an untaught concept.

| **Level** | **New financial concept** | **Earlier skill reused and later transfer** |
| --- | --- | --- |
| 1 Grocery List | Earning, spending, saving; needs and wants | Separate earned money from a grocery allocation; price comparison returns in Levels 3, 4, and 6 |
| 2 First Account | A repeatable budget; savings account and simple interest | Allocate earned money between current spending and saving; budget and interest reappear in Levels 3 and 5 |
| 3 Surprise Week | Unexpected expenses, accessible buffer, liquidity risk | Repair the Level 2 budget and use Level 1 saving; access to money is retested in Level 5 |
| 4 Prices Move | Inflation and purchasing power | Revise a saved goal and budget when prices rise; compare nominal and real outcomes in Levels 5 and 7 |
| 5 Lock or Keep Ready | Fixed deposit, time horizon, interest growth | Reuse the buffer and inflation lens while comparing interest with access; the tradeoff returns in Level 7 |
| 6 First Pay Slip | Net versus gross, tax on receipts, payment verification | Budget from money actually received; reconcile earnings and expenses before the final fair |
| 7 Future Fair | Investment risk, diversification, compounding, suspicious offers | Combine earning, budgeting, saving, inflation, interest, and liquidity to protect a goal |

### Money goals by map

| **Map** | **Core concept** | **Default money objective** |
| --- | --- | --- |
| 1 The Grocery List | Earn, spend, save | Save ₹100 after a complete grocery trip |
| 2 The First Account | Budget, account, interest | Earn ₹1,000 from simulated tasks and save a chosen share; the stipend context has an equivalent income goal |
| 3 The Surprise Week | Emergency buffer, liquidity | Finish the week with a ₹300 accessible buffer |
| 4 Prices Move | Inflation, goal cost | Build a ₹1,500 goal fund at the updated price |
| 5 Lock or Keep Ready | Fixed deposit, interest, risk | Reach ₹2,500 in total goal funds while retaining the required accessible buffer |
| 6 The First Pay Slip | Gross and net, GST, UPI | Reach ₹3,500 in verified cumulative in game inflows and reconcile the ledger |
| 7 The Future Fair | Diversification, investment risk, compounding | Reach a ₹5,000 protected goal fund; market gains do not count toward the gate |

Level 1 distinguishes money earned in a supervised school task from money allocated for groceries. The default Level 2 path earns ₹1,000 through further scripted, age appropriate tasks. An alternate fictional stipend path has an equivalent income goal, labelled as a stipend rather than earnings. Interest is excluded from both targets. Savings account interest adds a small amount over stated time; it cannot plausibly turn ₹100 into ₹150 immediately. That balance can follow a later deposit plus illustrative interest. In Level 7, volatile investment gains do not count toward the money gate, so excessive risk never becomes the easiest path.

### Two gate progression controller

Gate 1 checks the map's money objective. If it is unmet, the player keeps exploring that map. When it is met, Gate 2 checks only demerits whose concepts have already been taught. A maximum applicable demerit below 30 clears the map. A score from 30 to 59 opens the next map but schedules a relevant bottleneck there. A score of 60 or more routes the player to a short repair scene at the source map before advancing. A serious simulated credential-sharing choice can also trigger immediate safety repair. These are tuning values for playtesting, not claims about a student's real behavior.

The controller uses evidence from more than one decision for ordinary mandatory repair. A player who reaches ₹100 through poor price comparisons may therefore advance and meet a personalized consequence later. The same player can earn ₹1,000 in Level 2 yet still need to repair an earlier spending habit. A weight becomes eligible only after its concept has appeared in a choice, and its bottleneck tests that concept in a new setting. After repair, the earlier choice and every downstream balance are recomputed; the rupee reward is not duplicated. The three possible end states are advance, advance with bottleneck, and remediate.

## Persistent behavioral weights

The game silently carries five demerit scores from 0 to 100; higher means more evidence of an unresolved habit within the simulation. The first three are the central M1 dimensions. Scores start at 0, rise through observed choices, and decay after demonstrated improvement. A rule might add 15 for ignoring unit price or remove 10 after a sound comparison. Each update has a decision ID and explanation. Scores never stand in for real income, intelligence, or character. The player sees constructive feedback after a scene; raw demerit numbers remain internal.

| **Demerit** | **Evidence that raises it** | **Later event and repair** |
| --- | --- | --- |
| Overspending | Impulse buying beyond a plan or repeatedly ignoring full or unit cost | A later grocery or fair bill exposes the gap; revisit a price comparison |
| Cash concentration | For a long-horizon simulated goal, leaving every rupee idle or putting all investable money in one asset | An inflation or single-asset shock reveals exposure; revisit the goal horizon or mix |
| Risk mismanagement | No accessible buffer for a known near-term need, or excessive exposure to a volatile asset | A repair bill or market dip prompts a safer allocation for that goal |
| Saving inconsistency | Relying on one windfall while skipping a realistic repeatable plan | A goal falls short; return to the home deposit plan |
| Verification neglect | Ignoring sender, terms, recipient, or simulated UPI authorization | A timed suspicious offer points back to the source-checking scene |

**Context rule.** Cash kept for groceries, emergencies, or a near-term goal is not a cash-concentration fault. Diversification becomes relevant only after the learner knows investing, a longer horizon exists, and a buffer remains. Spending on a genuine need does not raise overspending. This keeps a weight from teaching the wrong lesson.

**Update and decay rule.** Deterministic JSON rules attach each change to a scene choice and its place in the concept ladder. Good later behavior reduces the corresponding demerit. On replay, the new choice replaces the old evidence instead of stacking points. A score can trigger a bottleneck only when its prerequisite was taught and its evidence is sufficient. The rules never penalize a learner for not knowing interest, inflation, or diversification before those topics appear.

## Map 1 The Grocery List

The player receives a grocery allocation and a small separate payment for a supervised school fair task. They are asked to buy groceries for a shared meal before the weekend study session. A tempting display and a changing price force them to distinguish money earned, money entrusted for an expense, money spent, and money saved.

**Money goal.** Complete the list and save ₹100. This is a small, achievable buffer, not a lesson in refusing every enjoyable purchase.

**New concept and earlier skill.** Earning, spending, and saving are three separate ledger actions. The player chooses needs before optional wants, then computes what remains after the entire trip.

**Decision that proves learning.** The grocery list is complete, the return trip is safe, and the player can point to the earned ₹100 separately from unspent household money. A later unfamiliar basket tests the same distinction.

### Buildings and visual novel interactions

**Home.** Farah reads the grocery list with the player and separates a school task payment from the grocery allocation. Choose what is essential today, what can wait, and whether to note a spending limit. Returning here reveals what the household can actually cook.

**Kirana shop.** The shopkeeper shows unit prices, a bundle offer, and a flashy snack by the counter. The player can compare quantity and total cost, switch brands, or buy impulsively. Overspending means a listed ingredient is missing, leading to a return trip or adjusted meal.

**Vegetable stall.** The player compares fresh produce prices and quantities. The lesson is that the cheapest sticker is not always the best value when quantity or waste differs.

**Bus stop.** A travel cost appears on the return route. If the player spent the entire budget, they must choose a safe alternative or revise a purchase. Travel is treated as a possible need, not automatically a want.

**Home return.** Ms Nandini asks, 'What did the plan forget?' The ledger shows the original list, actual basket, travel, and remaining cash. The player can rewind a shop decision and compare both outcomes.

**Possible later bottleneck.** If overspending is high, Map 3 may include a missing essential because the player focused on a bundle price but ignored the total basket. The repair link returns to the kirana scene.

**Player takeaway.** Earning, spending, and saving are distinct; needs versus wants are contextual; compare total and unit cost before deciding what remains.

## Map 2 The First Account

The learning center pays the group for approved fair preparation tasks, or the chosen stipend context supplies equivalent in game inflows. Teja wants to keep every rupee as cash; the player explores what a savings account changes and what it does not.

**Money goal.** Earn ₹1,000 through legitimate simulated tasks, then choose a sustainable deposit. The alternate stipend context has an equivalent income goal. The ledger excludes deposits, transfers, and interest from the target; interest is a small, time based addition.

**New concept and earlier skill.** Build a simple budget from earned inflows, planned needs, and a repeatable savings amount. Learn what a savings account does and read simple interest as a separate, time dependent entry.

**Decision that proves learning.** The player earns the ₹1,000 goal, explains why a transfer is not new earnings, and sets a deposit that still covers known travel or data costs. The plan faces an unexpected expense in Level 3.

### Buildings and visual novel interactions

**School project desk.** Choose among safe fictional tasks such as poster design, inventory counting for the fair, or proofreading an English notice. The student sees time required and agreed payment before accepting. No real employment or payment is involved.

**Bank branch.** A staff character explains account purpose, deposits, withdrawals, interest, and what documents or guardian support may depend on age and bank rules. The player asks questions and selects a fictional account pathway; no real account is opened.

**Passbook kiosk.** The player sorts deposits, withdrawals, and interest entries in a sample statement. A later deposit takes a sample balance to ₹150; the ledger separates deposited money from earned interest.

**Home desk.** Arrange earned inflows, essential spending, optional spending, and a repeatable savings amount into a simple weekly budget. The player sees how an unrealistic plan can fail when transport or data costs are due, and can adjust without penalty.

**School courtyard.** Farah asks if keeping cash at home is always wrong. The player explains that accessibility and security depend on circumstances; the story does not shame someone who cannot use an account yet.

**Possible later bottleneck.** If saving inconsistency is high, a later goal will fall short because the player deposited only a one time windfall. The repair link returns to the home desk to choose a repeatable amount.

**Player takeaway.** Know where money came from, understand a savings account, read a statement, and separate deposits from interest.

## Map 3 The Surprise Week

Just before a school event, a bicycle or phone needs repair. The event is unavoidable but its timing is unknown. The level tests whether the player's earlier savings are available when needed.

**Money goal.** Meet the necessary expense and finish with a ₹300 accessible buffer. The player may move a goal date rather than take a risky shortcut.

**New concept and earlier skill.** A budget needs room for unexpected expenses. Saving is useful when enough of it remains accessible; risk includes the possibility of being unable to pay a near-term need.

**Decision that proves learning.** After a surprise bill, the player revises the Level 2 budget, pays for the necessary repair or care, and explains why the remaining ₹300 must be available when needed.

### Buildings and visual novel interactions

**Repair lane.** Two repair quotes differ in price, quality, and waiting time. The player asks what is included. A very cheap option may need a return visit; a costly option may delay the goal.

**Clinic and pharmacy.** An alternate persona scene uses a small unplanned health expense instead of the device repair. The player must keep the essential purchase in the plan; the game never rewards avoiding needed care.

**Bank kiosk.** Check the accessible savings balance and plan a withdrawal for the repair. The player sees the revised balance and goal date before confirming.

**Home.** The player reprioritizes the next two weeks. A revised plan can preserve the goal at a later date. A rewind comparison shows the cost of having no buffer and the cost of holding all money idle.

**Learning center.** Ms Nandini asks the player to explain why emergency money must be easy to reach. The answer is expressed through a choice in the next scene, not a pass or fail quiz gate.

**Possible later bottleneck.** High overspending, saving inconsistency, or risk mismanagement produces different repair variants. The player revisits the basket, deposit plan, or buffer decision that caused the event.

**Player takeaway.** An emergency buffer is for uncertain near term needs; a plan can recover after a setback.

## Map 4 Prices Move

Several weeks later, the price of the player's chosen goal has changed. Farah's commute and the group's fair supplies also cost more. The player sees why a saved rupee amount and the purchasing power of that amount are different.

**Money goal.** Build a ₹1,500 goal fund after the revised price is disclosed. The number is a fictional default and scales with the chosen context.

**New concept and earlier skill.** Inflation can reduce purchasing power even while a rupee balance stays the same. The player distinguishes one changed price from a wider pattern and revises a budget and savings goal.

**Decision that proves learning.** Given an old and a new price, the player identifies the changed goal gap and chooses a workable adjustment to spending, saving rate, goal size, or date. Level 5 revisits this when interest is shown.

### Buildings and visual novel interactions

**Market lane.** A familiar item now costs more. The player compares an old receipt with a new one and separates a one item price change from the broader idea of inflation.

**Bus and metro kiosk.** A fare and a travel time tradeoff change the weekly budget. The player decides which route still supports school attendance and the savings goal.

**Copy shop.** The fair needs printing. The player compares per page and package prices, including an extra service fee. A cheap headline price can become dearer at checkout.

**School noticeboard.** The player's original goal price is crossed out and replaced. They can increase periodic saving, choose a lower cost path, or move the target date. All three are valid if the tradeoff is understood.

**Home.** The ledger shows nominal rupees and what they can buy in this fictional market. The player replays an earlier plan to see how the revised price changes the outcome.

**Possible later bottleneck.** High saving inconsistency or overspending can make the higher price feel impossible. For a long-horizon future lens, high cash concentration can produce a bounded inflation shock. The event teaches purchasing power here; investment diversification is examined only after Map 7 introduces it.

**Player takeaway.** Inflation changes purchasing power; goals need a price, date, and revision plan.

## Map 5 Lock or Keep Ready

With a larger goal fund, Teja hears that a fixed deposit can pay more interest than ordinary savings in the game's illustrative terms. A future expense may occur before the deposit matures. The player must decide what money can be locked and for how long.

**Money goal.** Reach ₹2,500 across accessible and goal funds while maintaining the required accessible buffer. Locked money is shown separately from cash available today.

**New concept and earlier skill.** Interest grows over time, but access, term, and inflation change what a higher quoted rate means for a real goal. The scene introduces the idea of growth on prior growth without claiming a guaranteed outcome.

**Decision that proves learning.** The player reads principal, contributions, and interest separately, compares illustrative outcomes over time, and chooses a bank split that preserves the Level 3 buffer before a surprise expense tests it.

### Buildings and visual novel interactions

**Bank savings desk.** Compare fictional savings and fixed deposit cards by rate, term, access, and early withdrawal condition. The player can split funds instead of choosing all or nothing.

**Time window.** Move a timeline slider to see simple and compound illustrative interest over weeks or years. The display separates principal, new deposits, and interest on earlier interest. A higher nominal balance still does not settle the inflation question.

**Repair lane return.** A conditional repair bill tests access. If too much was locked, the player may use an early withdrawal route with its stated consequence or return to the bank choice.

**Home.** The player writes a short term and long term goal on a calendar, then assigns accessible and time bound funds. The scene ends with an explicit comparison of both strategies.

**Possible later bottleneck.** High risk mismanagement creates an early expense when too much money was locked. The backtrack points to the exact bank split decision. It cannot demand that the player abandon essential spending.

**Player takeaway.** A fixed deposit is time bound; access and possible early withdrawal cost matter as much as the displayed interest rate.

## Map 6 The First Pay Slip

The fair hosts a future life booth. In a clearly marked future age simulation, the player receives a first payslip and runs a small event stall. This makes taxes, receipts, and digital payments visible without pretending the current student owes the illustrated tax.

**Money goal.** Reach ₹3,500 in verified cumulative in game inflows and reconcile gross amount, deductions, net receipt, and spending. No tax rate or filing rule is presented as universal.

**New concept and earlier skill.** A budget uses the net money actually received. The player distinguishes gross pay, deductions, net pay, revenue, profit, and tax shown on a purchase, while verifying a simulated digital payment.

**Decision that proves learning.** The player balances the ledger using net receipts, checks a final price including stated tax, and rejects a payment request that does not match the intended transaction. The final fair tests these skills again.

### Buildings and visual novel interactions

**Future workplace booth.** A fictional payslip shows gross pay, labelled deductions, and amount received. The player identifies what actually enters the wallet; a wrong assumption causes a later budget shortfall.

**Fair stall.** The player prices a simple school fair product with materials and a small margin. Revenue is not profit. Unsold stock and repeat purchases change the result.

**Receipt counter.** Compare a sample receipt with and without GST shown where applicable. The player checks final payable amount and understands that tax shown on a bill is different from income tax.

**UPI booth.** A simulated QR payment and collect request appear. The player checks the recipient and authorization screen. A request for a PIN or OTP is a warning; no real credential is entered.

**Home ledger.** The player reconciles all inflows and outflows, then corrects the next plan. A friendly summary distinguishes earning, receiving, saving, and profit.

**Possible later bottleneck.** High verification neglect leads to an ambiguous payment request in Map 7. High overspending can expose a gross versus net budgeting error. Each repair returns to the relevant booth.

**Player takeaway.** Read gross and net amounts, distinguish revenue from profit, inspect a receipt, and verify a digital payment.

## Map 7 The Future Fair

The fair opens. Teja presents a market simulator, and Aditi receives an urgent message promising a scholarship and extraordinary returns. The player must protect the real goal, explain uncertainty, and help the group finish its Money Wall.

**Money goal.** Reach a ₹5,000 protected goal fund through planned additions and legitimate receipts. Market returns are excluded from the gate. The final scene unlocks when the money goal is met, even if the path includes a repair loop.

**New concept and earlier skill.** Investment outcomes vary; diversification can reduce one kind of concentration risk but cannot remove loss. Compounding depends on time and assumptions. The final decision combines the entire ladder with scam verification.

**Decision that proves learning.** The player compares an adverse and a favorable fictional market path, protects the near-term goal, explains a diversified versus concentrated choice, and verifies a suspicious offer without sharing credentials.

### Buildings and visual novel interactions

**Future Lab.** The player allocates a tiny fictional amount among cash, a deposit, and imaginary company shares. A time jump reveals more than one possible market path, including loss. The player inspects time horizon and downside before choosing.

**Library.** A simple chart compares one fictional stock with a diversified basket and labels fees. The player can rebalance a plan, but no real ticker or product is shown. The scene explains that diversification reduces some concentration risk without guaranteeing profit.

**Cyber café.** Aditi shows a scholarship message requiring an upfront fee, plus a separate offer claiming guaranteed fast returns. The player examines the sender, terms, source, and pressure tactics, then chooses a safe verification path.

**Bank and UPI kiosk.** The scam may produce a fake collect request. The player must distinguish receiving money from authorizing a payment. The game never displays or asks for a real PIN or OTP.

**Fairground.** A final linked choice combines the goal date, an unexpected cost, a market downturn, and the suspicious offer. The Money Wall compares two or three paths and records what the player would do differently.

**Possible later bottleneck.** The event selector uses the highest eligible demerit and current assets to form a safe event set. The AI personalizes one event, then a repair pin sends the player to the precise earlier scene. After repair, the player resumes the fairground with the updated plan.

**Player takeaway.** Stock ownership carries uncertainty; time, fees, and diversification affect outcomes; compounding is a mathematical effect, not a promise; suspicious offers deserve verification.

## How the dynamic bottleneck works

The event generator runs after each choice and at a level boundary. A rule-based selector reads the five demerits, current wallet and asset mix, goal date, and the concept ladder. It can use only concepts the player has met, then forms eligible event types and severity bands, such as a surprise bill after budgeting, a changed price after inflation, or a market dip after investing. The AI director selects and personalizes a narrative variant and bounded scenario parameters. Players who reach the same rupee target by different paths can face different later problems. Variation makes a posted walkthrough less useful; it does not require every playthrough to be unique.

| **Stage** | **Example** |
| --- | --- |
| Evidence | Map 5 fixed deposit choices tied up nearly all goal money; risk mismanagement demerit 42 with two supporting decisions. |
| Rule selector | Eligible event: Map 7 surprise expense, medium severity, using the approved repair and bank scene pattern. |
| AI personalization | Choose the repair context and Teja's dialogue within approved choices and amount bounds. |
| Generated dialogue | Teja says the repair shop needs payment today. The bank screen shows what is accessible and what is time bound. |
| Repair route | A map pin returns the player to the Map 5 bank split; a revised split replaces the prior decision. |
| Recalculation | The simulation recomputes the branch, updates demerits and balances, and resumes Map 7 at the paused scene. |

**Numerical authority.** The deterministic engine computes every balance, interest result, inflation effect, fictional tax amount, weight update, and gate decision. The model can propose only a value inside a preapproved parameter range; it cannot invent a financial rate, real product recommendation, penalty, or impossible gate.

**Structured output.** Each event proposal includes targetDemerit, evidenceDecisionIds, prerequisiteSceneId, eventType, severityBand, templateId, narrativeVariant, boundedParameterIds, choiceIds, explanationId, and returnCheckpoint. A validator rejects missing evidence, contradictory balances, unapproved text, or an event outside the selector's bounds. Offline play uses an authored fallback from the same event library.

**No soft locks.** At most one mandatory bottleneck appears at a time. A return scene always has a valid solution with the resources provided. Completing it resolves that bottleneck; the player cannot be sent through the same loop indefinitely. If the student needs support, Ms Nandini offers a hint and a simpler version of the same choice.

### Worked path from money gate to repair

In Map 1, the player begins a fictional trip with ₹600, comprising a ₹500 grocery allocation and ₹100 earned in a school task. They buy listed groceries for ₹400, pay ₹50 for travel, and accept a ₹50 bundle without checking unit quantity. They also skip a second price comparison at the vegetable stall. They return with ₹100, so Gate 1 clears. Two documented cost-comparison mistakes raise overspending demerit to 30; Gate 2 lets them enter Map 2 with a scheduled bottleneck. In Map 3, the selector chooses a refill cost before a repair, and the AI makes the scene refer to the earlier basket. The repair pin returns to the kirana scene. A better choice replaces the old evidence, recalculates later balances, and resumes Map 3. The replay never pays the ₹100 again.

In Level 2, the player then records ₹1,000 from approved simulated tasks, puts part into the account, and distinguishes that earned total from the account balance and its small interest entry. They complete the money goal, but the Level 1 overspending evidence still shapes the Level 3 surprise expense. The repair tests whether the Level 1 price comparison skill transfers into a new budget; earning more has not erased the earlier decision pattern.

A different player may reach Map 5 with a sound grocery plan but lock nearly every rupee in a fixed deposit. Their later event concerns accessible cash, and the repair pin leads to the Map 5 bank split. Another player with a long-horizon cash-only plan may first see the effect of inflation in Map 4, then compare diversified fictional assets in Map 7. Keeping an emergency buffer is never marked as a fault.

## State, replay, and fairness

| **State field** | **Purpose** |
| --- | --- |
| Wallet and account ledgers | Track spendable cash, accessible bank funds, time bound deposit, and fictional investment value separately. |
| Cumulative legitimate inflows | Support earning gates without counting borrowed or scam money as earnings. |
| Goal and price timeline | Record chosen goal, date, current fictional price, and inflation assumptions. |
| Decision and concept log | Store scene ID, choice, concept taught, concept reused, rationale, effect, and version for accurate replay and AI evidence. |
| Five demerits and history | Track observed habits, evidence IDs, thresholds, and decay after improved choices. |
| Level snapshots | Recompute downstream outcomes after a rewind; prevent replay farming and duplicate interest. |
| Accessibility and context | Select text/audio preferences, accessibility settings, and scenario money scale without storing real income. |

On replay, the player chooses a new branch at an earlier snapshot. All downstream fictional balances and events are recomputed from that point. The old path remains in a read only comparison view but awards no extra money. A player can finish with a lower balance after making a more thoughtful decision; the game explains the tradeoff rather than declaring the choice wrong. Market outcomes use fixed story seeds per comparison so the player learns from decisions rather than a lucky reroll.

## Educational and cultural design

- Use Hyderabad details as ordinary life: kirana quantities, school commutes, local market prices, a bank branch, UPI screens, English signs, and a community learning center. Do not use poverty as a spectacle or assume all students share a household structure.

- Write every scene and interface label in clear English with stable text keys. Use icon and number support, English captions, and optional English narration for accessibility. Setup uses the fixed English experience with no language selector.

- Make an expense's role depend on context. Mobile data may support classes; travel may be essential; a celebration may have social value. Avoid a rigid needs versus wants label.

- All investment assets are fictional. Show at least an adverse and a favorable scenario, display fees and inflation separately, and label every projection as an assumption rather than a promise. No real ticker, stock tip, brokerage link, or portfolio recommendation appears.

- Use a simulated bank flow. Actual account eligibility and required documents vary with age and bank policy; the game teaches what to ask. FD and tax scenes use illustrative terms rather than current universal rates.

- No real OTP, UPI PIN, Aadhaar number, account number, or family income is requested. The facilitator sees progress and concept support needs, not private financial answers.

## NGO delivery and evaluation

A facilitator creates an anonymous class code, loads a content pack, and lets students play on shared or personal Android devices. The app works offline for the authored game and queues anonymous progress for later sync. The AI director uses a compact local fallback when connectivity is absent. Teachers can pause after a map for a two minute discussion prompt without reading students' private choices aloud.

| **Measure** | **What the facilitator sees** |
| --- | --- |
| Reach | Activated learners, started maps, completed maps, and time per map |
| Understanding | Percentage who apply earning, saving, budgeting, inflation, interest, or risk in a later transfer scene rather than repeat a same-scene quiz |
| Repair | Which bottlenecks occur, resolve, or need a hint; no public ranking of students |
| Accessibility | Text/audio preferences, offline completion, and content load failures |
| Safety | Whether the learner checks suspicious offers and protects simulated credentials |

The strongest learning test is transfer: a player who budgets correctly at the kirana shop later handles an unrelated fair expense without being prompted with the same wording. Playtesting should check this across English accessibility modes and money contexts. Rewards are awarded for reasoning, recovery, and verification, never for ending with the largest balance or taking the highest risk.

## Implementation blueprint

| **Layer** | **Responsibility** |
| --- | --- |
| Godot 4.x Standard client | GDScript with Compatibility rendering; layered 2D town, normalized movement and collisions, entrance areas, native visual novel Controls, HUD, local saves and offline replay; Windows, Android and web exports |
| JSON content engine | Data-only level graphs under `game/content/` (Godot `res://content/`), stable IDs, prerequisites, English text keys, choice effects and authored event fallbacks; no executable chapter scripts |
| Deterministic GDScript game engine | Exact integer-paise arithmetic, time steps, asset values, interest assumptions, weight rules with decay, two gates, branch recalculation and validated effect-ID dispatch |
| Hybrid event generator | Rules set event type and severity from demerits and portfolio; AI personalizes bounded narrative and parameters |
| Optional HTTPS service | Stable JSON contracts for pseudonymous sessions, cloud saves, approved AI events and facilitator aggregates; no client stack dependency and no network requirement for authored gameplay |
| Offline and safety layer | Cached authored events, validated model output, content versioning, and sync after connectivity returns |

Build a runnable foundation in Phase 0, then add one complete level per phase in Phases 1–7. Preview the actual integrated game after every phase, including its new content and the previously completed levels. Phase 3 completes the first cross-map repair path: a Map 1 money pass with overspending evidence, Map 2 task earnings, a Map 3 event, a return to Map 1, and resumption in Map 3. Later phases add the remaining levels using the same JSON and simulation contracts. The production system must work with its offline authored fallback when an AI request fails. Preview evidence and the release gates are specified in [phases.md](phases.md).

Every preview runs the current Godot build rather than a mockup. Validate keyboard and touch chapter entry, pause and resume, the newest complete level, and earlier levels on the same integrated project. The visual novel lives in a `CanvasLayer` with native `Control` containers, an interior `TextureRect`, staged character sprites, `RichTextLabel` dialogue and `Button` choices. Its `choice_selected(scene_id, choice_id)` signal requests a deterministic result; the UI displays that result without applying money itself. SaveManager commits the decision, snapshot, active branch, content version and post-choice resume beat as one validated aggregate, with temporary-file checks and a prior valid backup; recovery must not duplicate a payment. Filesystem operations are not assumed to provide database transaction guarantees.

## Planned testing methodology

| **Test** | **Pass condition** |
| --- | --- |
| Unit tests | Deterministic choice sequences produce expected wallet, asset, demerit vector, decay, Gate 1, Gate 2, and concept eligibility results. |
| Bot simulations | Reckless spender, cash-heavy long-horizon learner, and concentrated-risk learner receive distinct eligible events; each can repair and complete every map. |
| Path divergence | Different histories at the same money gate lead to meaningfully different event types or dialogue while equivalent choices remain reproducible for debugging. |
| Event safety | Generated English text cannot change engine numbers or exceed bounds; financial claims, age context, and clarity pass review. |
| Usability and performance | Godot Windows and midrange Android playtests check navigation, save/resume, offline play, load time, and whether students understand why an event occurred. Optional web exports require separate browser controls, caching and persistence checks. |
| Concept ladder | Each of the eight named concepts appears in an active choice, a visible consequence, and at least one later transfer scene; no bottleneck uses an untaught concept. |
| Pilot learning | Later transfer scenes show concept-score improvement without simply repeating the original question. |

Release criteria are correct gates and weight behavior for all simulated profiles, no unreachable money target or repair loop, measurable learning gains in the pilot, and positive usability feedback. Event variation is a measured design goal; the game does not claim that every possible run is unique.

## Reference notes

The concept follows the supplied Vanguard India brief and the M1 approach. Financial details should be checked against current primary guidance before release.

[RBI  Accounts for minors](https://www.rbi.org.in/Scripts/NotificationUser.aspx?Id=9862)

[RBI  Financial Awareness Messages on deposits](https://www.rbi.org.in/commonperson/images/FAME202426022024.pdf)

[NPCI  UPI safety and PIN guidance](https://www.npci.org.in/what-we-do/upi/faqs)

[SEBI  Money Matters learning topics](https://investor.sebi.gov.in/moneymatters.html)

[SEBI  How to spot a scam](https://investor.sebi.gov.in/spot-any-scam.html)

[CBIC  Tax invoice rules](https://cbic-gst.gov.in/gst-invoice-rules.html)

[Vanguard  Investing goals and risk](https://investor.vanguard.com/investor-resources-education/how-to-invest)

