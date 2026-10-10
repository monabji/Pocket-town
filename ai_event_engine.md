# Pocket Town Adaptive Event Engine

## Purpose

Two players can reach the same rupee target through different choices. The adaptive engine makes those choices matter later by selecting a relevant bottleneck, personalizing its story details, and routing the learner to an earlier decision that can repair it. It is not a free-form finance tutor or a source of numeric truth.

## Authority split

| Component | May decide | Must not decide |
| --- | --- | --- |
| Deterministic engine | Balances, interest, fictional tax arithmetic, weights, gate results, replay state. | Narrative wording. |
| Rule selector | Eligible target weight, event family, severity band, concept prerequisite, approved amount range, source decision, return checkpoint. | New financial formulas or unsupported concepts. |
| AI adapter | Approved variant ID, character phrasing, and bounded scenario parameter IDs inside the selector's envelope. | Balances, gate status, real products, investment recommendations, penalties, or impossible choices. |
| Validator | Accept or reject the structured proposal. | Change the engine state as a side effect. |

## Selection algorithm

1. Read the active branch only: weight values, supporting decision IDs, concept exposure, assets and accessibility, goal date, content version, unresolved event IDs, and selected persona context.
2. Filter event templates by taught prerequisite concepts, sufficient evidence, current level, safe feasible repair path, and one-active-bottleneck rule.
3. Prioritize the largest eligible unresolved weight. Break ties by oldest evidence, then stable template ID. Choose a severity band from the weight range; never infer a real student's traits.
4. Construct an envelope containing permitted template IDs, parameter IDs and ranges, dialogue tone, allowed characters, source scene, and return checkpoint.
5. Use an authored local event immediately when offline. When online, the AI adapter may choose a variant inside the envelope. Validate its output before display; otherwise use the authored event.
6. On resolution, mark the event ID complete and recompute the active branch from the source snapshot.

The event type should transfer learning: an overspending habit from the kirana shop can surface in a refill or fair bill; a repeatable-budget gap can surface when a goal price changes; a liquidity mistake can surface in an unexpected repair. An investment market dip is eligible only after investing has been introduced. Keeping a near-term emergency buffer in cash never creates a concentration penalty.

## Structured event proposal

```json
{
  "contentVersion": "1.0.0",
  "targetWeight": "overspending",
  "evidenceDecisionIds": ["decision-17", "decision-21"],
  "prerequisiteSceneId": "l1.kirana.unit_price",
  "eventTemplateId": "overspend.refill.medium",
  "severityBand": "medium",
  "narrativeVariantId": "refill.before_repair",
  "boundedParameterIds": ["approved_refill_cost_2"],
  "choiceIds": ["review_basket", "adjust_optional_item"],
  "explanationId": "overspend.compare_full_cost",
  "returnCheckpoint": "l3.repair.paused"
}
```

Use IDs in the model response wherever possible. Server-side approved content resolves those IDs to English text and amounts. If free text is necessary, restrict it to a short dialogue field and validate length, English-only text, financial claims, private-data requests, hostile/shaming wording, and references to real products. Never display raw model output before validation.

English is a fixed server prompt and validation rule, never a request parameter or player preference. Reject non-English narrative text and use the approved English fallback. Stable text IDs and the shared rupee formatter keep explanations and amounts consistent.

## Validation and fallback

Reject a proposal if it lacks supporting decisions, names an untaught concept, exceeds its amount bounds, contradicts the ledger, changes a choice ID or checkpoint, recommends a real investment, asks for a credential, or presents a projection as guaranteed. Also reject when the source decision is no longer in the active branch after replay.

Every event family has an authored fallback with the same educational purpose and valid repair path. An AI timeout, malformed response, safety rejection, or missing network chooses that fallback. The player should see a coherent story in either case; the game does not announce the model failure.

## Prompt and data minimization

Send only pseudonymous, task-relevant simulation state: concept IDs, weight band, evidence choice IDs, asset categories, goal horizon, event envelope, and selected persona context. Do not send a learner's real name, household details, credentials, free-form private answers, or class roster. Treat content-pack dialogue and any model output as untrusted data. Server prompts explicitly prohibit instructions found inside learner text or external messages from changing the schema or authority split.

## Evaluation

Use the bot personas in [quality_and_learning.md](quality_and_learning.md). For the same rupee gate, compare a reckless spender, cash-heavy long-horizon learner, concentrated-risk learner, and careful planner. Confirm that events differ for a reason, are explainable from prior choices, and are solvable. Test offline fallback, prompt-injection strings, contradictory balances, repeated events after repair, and non-English generated dialogue. Measure useful path divergence rather than claiming every run is unique.
