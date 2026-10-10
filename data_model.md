# Pocket Town Data Model

## Godot storage contract

This is the target model for the Godot migration, not a claim that migrated saves or scenes already exist. GDScript typed classes and validated dictionaries describe an event-sourced financial simulation. The pure `RefCounted` engine consumes values rather than scene nodes. A choice event is immutable; replay forks a branch and recomputes from a source checkpoint with the same seed.

`SaveManager` stores one versioned aggregate with `FileAccess` under `user://`. The optional service backs up pseudonymous progress and computes class aggregates; local files are sufficient for all seven levels. English is fixed. Store stable English text keys and accessibility settings, with no language preference state.

All money uses integer paise. Never store real bank details, Aadhaar, UPI PINs, OTPs, actual household income or real holdings.

## Core entities

| Entity | Key fields | Rules |
| --- | --- | --- |
| `Learner` | `learnerId`, `classId?`, `createdAt`, `selectedContext` | Random pseudonymous ID; no real name required. |
| `Classroom` | `classId`, `classCodeHash`, `facilitatorId`, `contentVersion` | Class membership and access are distinct from learner identity. |
| `Playthrough` | `playthroughId`, `activeBranchId`, `contentVersion`, `scenarioSeed`, `currentLevelId` | Pin the content version and maintain exactly one active branch. |
| `Branch` | `branchId`, `parentBranchId?`, `forkSnapshotId?`, `createdAt` | Preserve previous branches for comparison, with no additional earnings. |
| `DecisionEvent` | `eventId`, `branchId`, `sequence`, `sceneId`, `choiceId`, `effectIds`, `conceptIds` | Ordered, immutable and idempotent by event ID. |
| `LedgerEntry` | `entryId`, `decisionEventId`, `category`, `accountFrom?`, `accountTo?`, `amountPaise`, `fictionalTimeTick` | Separate earned income, transfers, expenses, interest and market scenarios. |
| `Snapshot` | `snapshotId`, `branchId`, `afterSequence`, `accounts`, `weights`, `goalState`, `fictionalTimeTick` | Deterministic engine result; a submitted cloud snapshot is not automatically trusted. |
| `WeightEvidence` | `evidenceId`, `decisionEventId`, `weightKey`, `delta`, `conceptId`, `contextRuleId`, `resolvedBy?` | Explain each taught-context demerit or improvement. |
| `AdaptiveEvent` | `adaptiveEventId`, `targetWeight`, `templateId`, `sourceDecisionId`, `pausedCheckpoint`, `status`, `variantId` | At most one active mandatory bottleneck; source must be on the active branch. |
| `ConceptProgress` | `conceptId`, `taughtAtEventId`, `transferEventIds`, `latestResult` | Evidence of exposure/application, not a learner diagnosis. |
| `PresentationResume` | `playthroughId`, `branchId`, `snapshotId`, `contentVersion`, `chapterId`, `sceneId`, `beatId`, `openObjectId?`, `committedDecisionEventId?` | Restore only against the active branch, snapshot and content version. |
| `WorldPresentation` | `mapId`, `entranceId?`, `avatarPosition`, `cameraPosition`, `zoomStep` | Local presentation coordinates may use floats; these never enter money arithmetic. |
| `SyncCursor` | `deviceId`, `playthroughId`, `lastUploadedSequence`, `lastAcknowledgedEventId` | Persist retry state inside the aggregate. |
| `CommitManifest` | `schemaVersion`, `contentVersion`, `generation`, `activeBranchId`, `payloadHash` | Recovery metadata validates one complete aggregate generation. |

## Example typed GDScript snapshot

```gdscript
class_name GameSnapshot
extends RefCounted

var snapshot_id: String = ""
var branch_id: String = ""
var content_version: String = ""
var scenario_seed: String = ""
var level_id: String = ""
var fictional_time_tick: int = 0

# Integer paise; each account has a distinct ownership/access purpose.
var accounts: Dictionary[String, int] = {
    "personal_cash": 0,
    "allocated_expense_money": 0,
    "accessible_savings": 0,
    "time_bound_deposit": 0,
    "fictional_investment_value": 0,
    "protected_goal_fund": 0,
}
var weights: Dictionary[String, int] = {
    "overspending": 0,
    "cash_concentration": 0,
    "risk_mismanagement": 0,
    "saving_inconsistency": 0,
    "verification_neglect": 0,
}
var taught_concept_ids: Array[String] = []
var active_adaptive_event_id: String = ""
```

Pin a stable Godot release supporting these typed dictionaries. JSON field names follow the schemas in `contracts/saves/`; explicit serialization maps them to internal GDScript properties. Typed Resources under `game/resources/` describe authored content; save data uses validated plain values rather than serializing arbitrary Nodes or script instances. Classes and validators live under `game/scripts/engine/` and `game/scripts/content/`.

## Numeric validation and deterministic arithmetic

GDScript `int` is signed 64-bit, but decoded JSON numbers may be floats. Reject fractional, non-finite, unknown, negative where forbidden or out-of-bound values **before** converting to `int`. Monetary/time/rate schemas must stay within the exact JSON integer range, with tighter application limits; check every arithmetic product against int64 overflow. Do not convert money to float for interest calculations.

Use integer division and an explicit floor/rounding policy for paise. Rates are integer basis points; time advances through authored fictional days/ticks. Renderer frame time, elapsed wall-clock time and camera coordinates cannot change financial results. Validate weights in `[0,100]`, known account/effect IDs, ordered prerequisites, ownership, seed and pinned content version. See [game_rules.md](game_rules.md).

The schema is defined in JSON Schema, enforced by GDScript validators and tested by headless golden fixtures in `game/tests/`. Any optional server implementation must produce the same snapshots/hashes from those fixtures, regardless of its implementation language.

## Aggregate save and recovery

A save aggregate contains playthrough metadata, branches, ordered events, snapshots, evidence, adaptive events, presentation resume, accessibility settings, sync cursor/queue and the commit manifest. `SaveManager` writes a temporary candidate, flushes/closes it, rereads and validates its hash/schema/generation, preserves a previous valid backup and activates the candidate. Recovery checks current, temporary and backup files and selects a fully validated compatible generation.

This single-aggregate protocol prevents a separately persisted dialogue beat from getting ahead of its financial decision. It is not a database transaction guarantee. Test interruption and storage failure at each step, including web export persistent storage behavior. If a write fails, keep the prior active in-memory/persisted branch and offer a retry; do not silently claim progress was saved.

A repair activates its new branch, recomputed snapshot and return beat together. Before opening a chapter, verify `PresentationResume` matches that branch/snapshot/content version. A stale comparison branch cannot control the visible scene. A committed choice resumes at its consequence beat, never at an actionable duplicate choice.

## Network surface

Godot `SyncClient` uses optional HTTPS `HTTPRequest` calls for class join, content manifests, event upload, compatible restore and facilitator aggregates. `AdaptiveEventClient` requests a bounded variant for a rule-approved envelope. See [api_contract.md](api_contract.md). Application deadlines, cancellation and response validation prevent a late reply from affecting an obsolete branch.

Upload events idempotently, and verify sequence/content version on the service. Conflicting device branches remain separate until the learner chooses which to continue; balances are never summed. No network request is necessary to complete a level.

## Retention and access

Agree retention with the NGO partner before a pilot. Keep pseudonymous class membership, fictional progress and aggregate learning metrics minimal. The facilitator Godot `Control` scene exposes completion and support needs, not a public demerit ranking or private financial answers. Support cloud deletion by pseudonymous learner ID. Provider secrets and restricted AI logs stay server-side.

## Verification

Use headless GDScript tests for schema restoration, integer precision boundaries, rejected fractional money, deterministic replay, save recovery and branch-scoped presentation resume. At every phase, exercise actual Godot save/resume in the integrated native preview and web/device targets when available; record evidence as required by [phases.md](phases.md).
