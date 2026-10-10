# Pocket Town API Contract

The API backs up pseudonymous progress and optionally personalizes a rule-approved adaptive event. It is not required for offline gameplay. All requests and responses are versioned JSON over HTTPS; the shared TypeScript schemas should generate client and server types.

## Common rules

- Every write has a client-generated `requestId` or event ID for idempotency.
- Every playthrough request includes `contentVersion`. A mismatch returns a structured version error; the client continues on its pinned local pack.
- Money values use integer paise. The server never trusts a client-supplied balance without checking the event sequence with the shared engine.
- Responses include a stable error code and a safe user-facing message key, not provider traces or prompts.
- Authenticate learners with a pseudonymous session token. A class code joins a classroom but is not an authentication secret.

English is fixed by the application contract. Endpoints have no language or locale parameter, and text keys resolve only to approved English strings. User-facing amounts use the shared rupee formatter.

## Endpoints

| Endpoint | Request | Response |
| --- | --- | --- |
| `POST /v1/classes/join` | `classCode`, `deviceId`, `requestId` | `learnerId`, short-lived session token, allowed content manifest. |
| `GET /v1/content/manifest` | Current content version | Available English pack versions, hashes, minimum app version. |
| `POST /v1/playthroughs/:id/events/batch` | Ordered choice events with IDs, branch ID, sequence, content version, optional snapshot hash | Highest acknowledged sequence and any conflict/version error. Duplicate IDs return the original acknowledgement. |
| `GET /v1/playthroughs/:id` | Playthrough ID and token | Branch metadata and compatible restore events/snapshot; never another learner's save. |
| `POST /v1/events/propose` | Rule selector envelope, evidence IDs, request ID | Validated variant/template IDs and parameter IDs or an authored fallback indicator. No balance or gate fields. |
| `GET /v1/classes/:id/aggregates` | Facilitator token, content version filter | Counts for reach, transfer, repair, safety, and offline completion; no raw demerit vector. |

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

The server acknowledges the event only if sequence and content IDs are valid. The snapshot hash is diagnostic; the server's recomputation is authoritative for cloud backup. The local app continues offline if sync fails.

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

The response contains only a chosen approved template/variant and parameter IDs, an explanation ID, and a validation result. Invalid or timed-out AI output produces the authored template chosen by the rules. See [ai_event_engine.md](ai_event_engine.md).

## Errors to handle explicitly

| Code | Client behavior |
| --- | --- |
| `NETWORK_UNAVAILABLE` | Queue the write and continue locally. |
| `CONTENT_VERSION_MISMATCH` | Keep using the pinned pack; offer an explicit migration only when available. |
| `SEQUENCE_CONFLICT` | Preserve both branches and ask which playthrough to continue; do not add balances together. |
| `INVALID_EVENT_PROPOSAL` | Render the authored fallback; do not expose raw provider output. |
| `SESSION_EXPIRED` | Renew the pseudonymous session when online; local play remains available. |

Before implementation, freeze JSON Schemas for these messages and add contract tests that run against both the mobile client types and the server handlers.
