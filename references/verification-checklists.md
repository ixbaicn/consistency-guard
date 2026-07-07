# Verification Checklists

Use these templates as graph-derived checklists. Mark every item as done, pending, not_applicable, or blocked.

## Preflight

```text
[ ] Capability negotiation completed
[ ] Verification mode declared as automated/manual/mixed/unsupported
[ ] Project profile or adapter loaded when available
[ ] Rule overrides loaded and evidenced when used
[ ] Non-goals declared
[ ] Evidence-first requirement acknowledged
[ ] Rollback strategy required for any edit
```

## Database Field Added

```text
[ ] Source of truth identified
[ ] Contract priority resolved
[ ] Source of truth has evidence
[ ] Protected domain scan completed
[ ] Migration classification recorded
[ ] Repair risk level and confidence recorded
[ ] Confidence has evidence
[ ] Rollback plan recorded
[ ] Migration updated
[ ] Actual schema reviewed when available
[ ] ORM/entity/model updated when present
[ ] Repository/query layer updated
[ ] Service/domain logic updated
[ ] DTO/request/response types updated
[ ] Validator updated
[ ] Permission and visibility reviewed
[ ] API handler/controller/resolver updated
[ ] OpenAPI/GraphQL/protobuf updated
[ ] SDK/client regenerated or manually updated
[ ] Frontend types updated
[ ] Form/display/table/search/filter updated when applicable
[ ] Cache shape/key/TTL/invalidation reviewed
[ ] Backfill/default/nullability decision recorded
[ ] Unit/integration/contract tests updated
[ ] Documentation updated
```

## Field Removed

```text
[ ] Removal is not actually a rename
[ ] Protected domain scan completed
[ ] Migration classification recorded
[ ] Repair risk level and confidence recorded
[ ] Confidence has evidence
[ ] Rollback plan recorded
[ ] Compatibility and deprecation policy reviewed
[ ] Migration updated
[ ] Backward compatibility decision recorded
[ ] Backend reads/writes removed or adapted
[ ] DTO/API spec/client/frontend types updated
[ ] Cache structure and invalidation updated
[ ] Tests updated for removal behavior
[ ] Documentation updated
```

## Field Renamed

```text
[ ] Rename confidence recorded
[ ] Protected domain scan completed
[ ] Migration classification recorded
[ ] Repair risk level recorded
[ ] Confidence has evidence
[ ] Rollback plan recorded
[ ] Compatibility strategy selected
[ ] Migration/backfill/alias strategy reviewed
[ ] Backend mapping updated
[ ] API request/response updated or aliased
[ ] SDK/client updated
[ ] Frontend consumers updated
[ ] Cache key/shape migration reviewed
[ ] Tests cover old/new behavior as required
[ ] Documentation and deprecation notes updated
```

## API Contract Changed

```text
[ ] API version impact identified
[ ] Protected domain scan completed
[ ] Repair risk level and confidence recorded
[ ] Confidence has evidence
[ ] Rollback plan recorded
[ ] Request schema updated
[ ] Response schema updated
[ ] Error contract reviewed
[ ] Backend handler/service/domain updated
[ ] OpenAPI/GraphQL/protobuf updated
[ ] SDK/client updated or regenerated
[ ] Frontend types and consumers updated
[ ] Contract tests updated
[ ] Documentation updated
```

## Business Rule Changed

```text
[ ] Rule source identified
[ ] Protected domain scan completed
[ ] Required approval recorded when applicable
[ ] Repair risk level and confidence recorded
[ ] Confidence has evidence
[ ] Rollback plan recorded
[ ] Controller/API boundary reviewed
[ ] Validator reviewed
[ ] Service/domain logic reviewed
[ ] Permission/auth reviewed
[ ] Background jobs reviewed
[ ] Cache invalidation reviewed
[ ] Tests updated for allowed and denied cases
[ ] Documentation updated
```

## Cache Contract Changed

```text
[ ] Cache keys identified
[ ] Protected domain scan completed
[ ] Cache write/read classification recorded
[ ] Repair risk level and confidence recorded
[ ] Confidence has evidence
[ ] Rollback plan recorded
[ ] Stored shape identified
[ ] TTL decision recorded
[ ] Invalidation paths updated
[ ] Versioning or migration strategy reviewed
[ ] Database/API shape compatibility reviewed
[ ] Tests or operational verification updated
[ ] Documentation updated
```

## Documentation Drift Review

```text
[ ] README examples match current API
[ ] OpenAPI/Swagger matches implementation
[ ] Architecture docs match current flow
[ ] Cache docs match current key and TTL policy
[ ] Migration or deployment notes updated
[ ] Compatibility/deprecation docs updated
```

## Repair Plan Review

```text
[ ] Every repair item has priority P0/P1/P2/P3
[ ] Every repair item has status done/pending/blocked/needs_approval/not_supported
[ ] Every repair item has risk level critical/high/medium/low
[ ] Every repair item has confidence
[ ] Every confidence value has evidence
[ ] Every executable repair item has rollback plan
[ ] Protected domains are blocked or approval-recorded
[ ] Readonly migrations are separated from writes
[ ] Transactions, locks, financial flows, queues, webhooks, and jobs are not auto-approved
[ ] Remaining risks are summarized by priority and category
[ ] Technical debt is summarized by kind
[ ] Blocked items record missing requirements
```

## Exit Criteria Review

```text
[ ] No known drift remains, or remaining drift is explicitly documented
[ ] Verification passed, or unavailable verification is downgraded to manual checklist
[ ] Protected domains were not modified without approval
[ ] Confidence is above configured threshold
[ ] Remaining risks are documented by priority and category
[ ] Rollback plan is ready for every change
[ ] Final status is consistent, blocked, or partial_with_risk
```
