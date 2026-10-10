# Pocket Town Data Model

## Storage principles

The active branch is an event-sourced simulation with periodic snapshots. A choice event is immutable; a replay forks from an earlier snapshot and creates a new active branch. Store only fictional game state and minimal classroom identifiers. The server backs up progress and produces aggregates, while the local database remains sufficient to play offline.

All monetary values are integer paise. Each record includes `contentVersion` and a stable scenario seed so a run can be reproduced. Do not store real bank account numbers, Aadhaar, income, UPI PINs, OTPs, or real product holdings.

English is fixed for all content and UI. Store stable English text keys in content; no learner language preference or translation-pack state exists. The shared display formatter renders integer paise consistently as rupees.

## Core entities

| Entity | Key fields | Notes |
| --- | --- | --- |
| `Learner` | `learnerId`, `classId?`, `createdAt`, `selectedContext` | Random pseudonymous ID; no real name required. |
| `Classroom` | `classId`, `classCodeHash`, `facilitatorId`, `contentVersion` | A class code joins sessions; never use the code as a learner identity. |
| `Playthrough` | `playthroughId`, `learnerId`, `activeBranchId`, `contentVersion`, `scenarioSeed`, `currentLevelId` | One save can contain multiple comparison branches. |
| `Branch` | `branchId`, `parentBranchId?`, `forkSnapshotId?`, `active`, `createdAt` | Exactly one active branch per playthrough. |
| `DecisionEvent` | `eventId`, `branchId`, `sequence`, `sceneId`, `choiceId`, `effectIds`, `conceptIds`, `createdAt` | Append-only, idempotent by `eventId`. |
| `LedgerEntry` | `entryId`, `decisionEventId`, `category`, `accountFrom?`, `accountTo?`, `amountPaise`, `fictionalTime` | Transfers balance; source category distinguishes earnings from interest. |
| `Snapshot` | `snapshotId`, `branchId`, `afterSequence`, `accounts`, `weights`, `goalState`, `fictionalTime` | Deterministic checkpoint for fast resume and repair. |
| `WeightEvidence` | `evidenceId`, `decisionEventId`, `weightKey`, `delta`, `conceptId`, `contextRuleId`, `resolvedBy?` | Explains every weight change and decay. |
| `AdaptiveEvent` | `adaptiveEventId`, `targetWeight`, `templateId`, `sourceDecisionId`, `pausedCheckpoint`, `status`, `variantId` | `scheduled`, `active`, or `resolved`; at most one active mandatory event. |
| `ConceptProgress` | `conceptId`, `taughtAtEventId`, `transferEventIds`, `latestResult` | Records exposure and later application, not a diagnosis. |
| `PresentationResume` | `playthroughId`, `branchId`, `snapshotId`, `contentVersion`, `chapterId`, `sceneId`, `beatId`, `openObjectId?`, `committedDecisionEventId?` | Restores the visual novel only against its active branch and pinned content. Commit the choice, resulting snapshot, and post-choice beat atomically. Camera position and optional avatar position are local presentation state. |
| `SyncCursor` | `deviceId`, `playthroughId`, `lastUploadedSequence`, `lastAcknowledgedEventId` | Supports offline batch retries. |

## Example TypeScript state

```ts
type MoneyPaise = number; // integer, validated at runtime
type WeightKey =
  | "overspending"
  | "cash_concentration"
  | "risk_mismanagement"
  | "saving_inconsistency"
  | "verification_neglect";

interface Accounts {
  personalCash: MoneyPaise;
  allocatedExpenseMoney: MoneyPaise;
  accessibleSavings: MoneyPaise;
  timeBoundDeposit: MoneyPaise;
  fictionalInvestmentValue: MoneyPaise;
  protectedGoalFund: MoneyPaise;
}

interface GameSnapshot {
  snapshotId: string;
  contentVersion: string;
  scenarioSeed: string;
  levelId: string;
  sceneId: string;
  fictionalTimeTick: number;
  accounts: Accounts;
  weights: Record<WeightKey, number>;
  taughtConceptIds: string[];
  activeAdaptiveEventId?: string;
}
```

Use runtime validation to reject noninteger money, negative balances where prohibited, out-of-range weights, unknown IDs, and unsupported content versions. The authoritative computation is in [game_rules.md](game_rules.md).

Before restoring a visual novel, verify that its resume record matches the active branch, snapshot, and pinned content version. A repair branch switch replaces the active resume record with the recomputed return checkpoint in the same local transaction that activates the new branch. Do not reuse an old branch's dialogue position or apply a committed choice again after interruption.

## API surface

| Endpoint | Purpose |
| --- | --- |
| `POST /v1/classes/join` | Exchange a class code for a pseudonymous learner session. |
| `GET /v1/content/manifest` | Return available signed English content versions. |
| `POST /v1/playthroughs/:id/events/batch` | Idempotently upload local decision and snapshot metadata. |
| `GET /v1/playthroughs/:id` | Restore a compatible saved branch. |
| `POST /v1/events/propose` | Request a bounded event variant for a selector-created envelope. |
| `GET /v1/classes/:id/aggregates` | Return facilitator progress without detailed personal choices. |

The API does not accept client-provided balances as unquestioned truth. It verifies sequence and content version or recomputes the branch using the shared engine. Sync uploads can be retried safely. Conflicting branches from two devices remain distinct until the learner selects which to continue.

## Retention and access

Define retention with the NGO partner before pilot deployment. The default design minimizes data: anonymous class membership, simulated choices, and aggregate learning metrics. A facilitator sees completion and support needs, not a public per-student demerit score or private financial answers. Provide a way to delete a learner's cloud save by pseudonymous ID. Keep provider keys and detailed AI logs server-side with limited access and a documented retention period.
