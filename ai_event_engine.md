# Pocket Town Adaptive Event Engine

## Purpose and Godot ownership

Two players can reach the same rupee target through different choices. The adaptive system uses earlier evidence to select a later bottleneck and return the learner to a repairable source decision. This document describes the Godot migration target; the engine is not a free-form finance tutor or a source of numerical truth.

Implement the deterministic rule selector as pure GDScript under `game/scripts/engine/`, using `RefCounted` state models and versioned content from `ContentRepository`. `GameSession` owns the active event and routes its chapter through `SceneRouter`. The actual building interaction remains a full visual novel with dialogue, objects, choices, consequences and safe replay.

The optional `AdaptiveEventClient` under `game/scripts/network/` uses HTTPS `HTTPRequest` to a contracted service. Provider execution and secrets remain server-side. The server language is not prescribed. Bundled English authored variants under `game/content/` support every eligible event offline.

## Authority split

| Component | May decide | Must not decide |
| --- | --- | --- |
| Pure financial engine | Integer-paise balances, interest, fictional arithmetic, weights, gates and recomputed branches. | Narrative phrasing or frame-based income. |
| Rule selector | Eligible target, severity, prerequisites, approved parameter bounds, source decision and return checkpoint. | New formulas, untaught concepts or unsupported penalties. |
| AI adapter | Approved variant IDs and bounded narrative parameters within the envelope. | Balances, gates, real investments, guaranteed outcomes or impossible repairs. |
| Validator | Accept or reject structured proposals against schemas and the active envelope. | Mutate financial state as a validation side effect. |
| Godot chapter runner | Present approved English beats and submit confirmed choices through `GameSession`. | Apply money directly or execute model text as code. |

## Selection algorithm

1. Read only the active branch: weights, supporting decisions, taught concepts, accessible funds, goal horizon, pinned content version, unresolved events and optional fictional context.
2. Filter templates by taught prerequisites, sufficient independent evidence, level timing, feasible source repair and the one-active-mandatory-event rule.
3. Prioritize the largest eligible unresolved weight, then oldest evidence and a stable key/template order. Do not infer a real student's traits.
4. Build a fixed envelope of allowed templates, parameters, characters, tone, source decision and return checkpoint.
5. Display an authored local event without needing network access. Online personalization may replace only the approved narrative variant before interaction begins; it cannot change already presented choices or numbers.
6. On repair, replace the source choice, recompute all downstream effects using the original seed, and persist the new active branch/snapshot/return beat as one aggregate before presenting it. Mark resolution only when the event's repair condition is met.

Weights 0–29 permit normal advancement; 30–59 schedule a relevant later bottleneck; 60–100 require immediate repair at the boundary. Ordinary mandatory repair requires more than one evidence decision. A critical simulated credential-sharing choice can trigger immediate safety repair independently of score. See [game_rules.md](game_rules.md).

Overspending at the kirana can surface in a later refill comparison; inconsistent saving can surface when a bill precedes income; liquidity mistakes can surface before a need's due date. Market scenarios become eligible only after investing is taught. A near-term emergency buffer in cash is exempt from concentration penalties.

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

Prefer approved IDs resolved to English text and integer paise. Free text, if introduced, is short and validated for length, English clarity, unsupported financial claims, private-data requests, shaming and real product recommendations. Never display raw provider text. English is a fixed prompt/validation constraint, not a player or request preference.

JSON Schemas belong in `contracts/api/` and `contracts/content/`; GDScript validators restore typed values only after integral numeric bounds checks. AI parameters are IDs or tightly bounded integers, never arbitrary formulas or script expressions. Paise arithmetic uses GDScript int64 with checked products and deterministic division; animation/physics time cannot affect adaptation or interest.

## Request lifecycle and fallback

Associate each request with its request ID, branch, snapshot and content version. `HTTPRequest` uses an explicit application deadline and emits completion through the network adapter. Cancel when the source branch becomes obsolete, and reject late replies after any repair or scene transition. A validator rechecks the envelope and active source at acceptance time.

Reject missing evidence, untaught concepts, out-of-bounds parameters, ledger contradictions, altered choice IDs/checkpoints, real product recommendations, credential requests and promised projections. Every event family has an authored equivalent with a valid repair path. Missing network, transport failure, deadline, malformed response, safety rejection or stale evidence selects that fallback without interrupting the chapter.

The engine and UI must remain usable if the service is absent. Saving uses the `SaveManager` temp/backup aggregate protocol, including the active adaptive event and post-choice resume state; it does not rely on a remote transaction. See [architecture.md](architecture.md), [data_model.md](data_model.md) and [api_contract.md](api_contract.md).

## Prompt and data minimization

Send pseudonymous task-relevant simulation facts only: taught concept IDs, weight band, evidence IDs, account categories, horizon and allowed envelope. Do not send names, household details, real credentials, private free-form answers or class rosters. Treat content dialogue and model responses as untrusted data. Server prompts reject embedded instructions that attempt to change the schema or authority split; client content is never executed.

## Evaluation and actual previews

Headless GDScript fixtures in `game/tests/` compare careful and impulsive routes with equal balances, stable tie-breaking, two-decision evidence, immediate safety overrides, deterministic branch repair and no duplicated pay/interest. Test invalid proposals, non-English dialogue, prompt injection, offline fallback, stale replies, one-event-at-a-time behavior and feasible source choices. Service tests use the same contract fixtures.

When adaptation is added in a phase, preview the real Godot game: trigger the event through prior decisions, enter its visual novel, return to the source building, replace the choice and resume the paused checkpoint. Demonstrate both network success and authored fallback where the optional service is available; offline fallback remains mandatory. Record native keyboard and web/device results when available, with screenshots/recording and issues before phase completion. See [phases.md](phases.md) and [quality_and_learning.md](quality_and_learning.md).
