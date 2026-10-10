# Pocket Town API Contract

## Godot client and optional service

This contract specifies the Godot migration target. The game runs offline with bundled English content and authored adaptive events; the optional service backs up pseudonymous progress, provides classroom aggregates and personalizes an approved event. The server implementation language and backing store are not fixed.

Godot `SyncClient` and `AdaptiveEventClient` under `game/scripts/network/` use `HTTPRequest` for versioned JSON over HTTPS. The facilitator is a Godot `Control` scene under `game/scenes/ui/` consuming aggregate endpoints. JSON Schemas live in `contracts/api/`; GDScript validation and language-independent golden fixtures enforce them on both sides. Do not assume generated language-specific client types are the contract.

## Common rules

- Each write uses a client-generated `requestId` or event ID for idempotency.
- Playthrough requests include pinned `contentVersion`, branch and sequence context. Version mismatch keeps the local pack active.
- Money is integer paise. Validate decoded numeric values as integral and bounded before converting to GDScript `int`; all JSON integers stay in the exact representation range, with tighter application limits.
- Never accept submitted balances without validating/recomputing their ordered decision sequence using the same deterministic fixtures as the Godot engine.
- Learners use pseudonymous session tokens. Class codes establish membership and are not sufficient authentication for every later request.
- Errors expose stable codes and approved English message keys, not provider traces, prompts or credentials.
- English is fixed; no language or locale request parameter exists. UI formatting converts paise to rupees only for presentation.
- Model provider keys and administrative secrets remain server-side. The client contains no AI provider credential.

## Godot request lifecycle

Create/request through a controlled network adapter, connect `request_completed` once, and check both transport result and HTTP status before parsing. Set bounded payload size and an explicit application deadline. The adapter emits typed success/failure signals; the financial engine never waits on a Node or network response.

Associate each adaptive request with active branch/snapshot/content version and request ID. Cancel obsolete requests on scene/branch changes and discard late replies even if transport cancellation races. Sync requests persist retry/idempotency metadata in the versioned save aggregate. Failure queues appropriate work without blocking local play. Never busy-wait in `_process` or `_physics_process`.

Web exports need HTTPS deployment and service CORS support for the deployed origin. Verify native desktop, web and device behavior when available. Do not assume a native networking/storage test proves browser export behavior.

## Endpoints

| Endpoint | Request | Response |
| --- | --- | --- |
| `POST /v1/classes/join` | `classCode`, `deviceId`, `requestId` | `learnerId`, short-lived session token and allowed content manifest. |
| `GET /v1/content/manifest` | Current content version | English pack versions, hashes, minimum game version and contract version. |
| `POST /v1/playthroughs/:id/events/batch` | Ordered events, IDs, branch, sequence, content version and optional snapshot hash | Highest acknowledged sequence or structured conflict/version error. Duplicates return the original acknowledgement. |
| `GET /v1/playthroughs/:id` | Playthrough ID and session | Compatible branch metadata and restore events/snapshot; never another learner's save. |
| `POST /v1/events/propose` | Rule envelope, evidence IDs and request ID | Approved variant/template and parameter IDs or authored fallback indicator. No balance/gate fields. |
| `GET /v1/classes/:id/aggregates` | Authorized facilitator session and optional content version filter | Completion, concept transfer, repair, safety and offline counts; no raw demerit vector. |

## Example sync request

```json
{
  "requestId": "sync-7b3f",
  "contentVersion": "1.0.0",
  "branchId": "branch-main",
  "events": [
    {
      "eventId": "choice-17",
      "sequence": 17,
      "sceneId": "l1.kirana.unit_price",
      "choiceId": "take_bundle",
      "scenarioSeed": "seed-42"
    }
  ],
  "snapshotHash": "sha256-of-client-snapshot"
}
```

The service checks sequence and content IDs before acknowledgement. The hash is diagnostic; validated event recomputation determines accepted cloud state. Local Godot play continues during sync failure. A server may use its own conforming implementation or a controlled headless rule runner, but must pass the same golden fixtures.

## Example event proposal request

```json
{
  "requestId": "event-23",
  "contentVersion": "1.0.0",
  "targetWeight": "overspending",
  "severityBand": "medium",
  "evidenceDecisionIds": ["choice-17", "choice-21"],
  "allowedTemplateIds": ["overspend.refill.medium"],
  "allowedParameterIds": ["approved_refill_cost_1", "approved_refill_cost_2"],
  "sourceSceneId": "l1.kirana.unit_price",
  "returnCheckpoint": "l3.repair.paused"
}
```

Response fields contain only approved template/variant, bounded parameter and explanation IDs with validation status. Approved English content resolves them. Invalid, late or timed-out output uses the rule-selected authored event. See [ai_event_engine.md](ai_event_engine.md).

## Errors and fallback

| Code | Godot behavior |
| --- | --- |
| `NETWORK_UNAVAILABLE` | Queue eligible sync and continue from local content/save. |
| `CONTENT_VERSION_MISMATCH` | Use the pinned pack; offer an explicit tested migration when available. |
| `SEQUENCE_CONFLICT` | Preserve both branches and ask which playthrough to continue; never add balances. |
| `INVALID_EVENT_PROPOSAL` | Display the authored fallback; never expose raw provider output. |
| `SESSION_EXPIRED` | Renew the pseudonymous session online while local play remains available. |
| `REQUEST_TIMEOUT` | End waiting UI, retain queued sync where relevant and use local fallback for adaptation. |

## Contract verification

Freeze schemas before integrating a service. Run GDScript headless fixtures for valid/invalid messages, numeric bounds, timeout handling, duplicate upload, branch conflicts, stale adaptive replies and offline fallback. Server tests consume the same fixtures. Every phase adding network behavior must show it working or falling back in the actual integrated Godot preview, with native/web/device coverage when available and recorded evidence under [phases.md](phases.md).
