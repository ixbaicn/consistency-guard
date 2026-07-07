# Drift Rules

Every finding must include category, severity, confidence, evidence, and required action.

## Categories

```yaml
drift_categories:
  schema: "Field, table, collection, relation, index, uniqueness, default, nullability, length, or enum mismatch."
  api: "Request, response, status code, error shape, route, resolver, OpenAPI, GraphQL, protobuf, or SDK mismatch."
  type: "Language or client type mismatch across duplicated representations."
  semantic: "Same field or rule has different meaning, lifecycle, default, validation, or transformation."
  business: "Business rule changed in one layer but not validators, services, permissions, jobs, or tests."
  cache: "Key, TTL, hash shape, JSON shape, invalidation, projection, or version mismatch."
  performance: "Query, index, pagination, payload, cache, or N+1 behavior changed inconsistently."
  compatibility: "Version, deprecation, rename, SDK, client, or migration compatibility mismatch."
  security: "Permission, visibility, authorization, sensitive field, or validation mismatch."
  documentation: "Docs, README, architecture notes, Swagger, or examples no longer match contracts."
  repair_safety: "Repair plan lacks protected-domain status, migration classification, confidence, risk level, or required approval."
```

## Severity

```yaml
severity:
  blocker:
    meaning: "Can cause data loss, security exposure, broken external contract, failed migration, or unrecoverable inconsistency."
  high:
    meaning: "Likely runtime failure, client breakage, invalid data, or production incident."
  medium:
    meaning: "Inconsistent behavior, missing coverage, stale generated artifact, or likely future bug."
  low:
    meaning: "Documentation, naming, or minor consistency issue with limited runtime risk."
```

Security drift involving authorization bypass, sensitive field exposure, tenant boundary leakage, or write access mismatch is blocker by default. Repair safety drift involving protected domains, financial transactions, locks, or unsupported transaction semantics is blocker by default.

## Human Decision Points

Enter NEED_HUMAN_DECISION when any required semantic choice is missing:

```yaml
missing_decisions:
  - default_value
  - nullable_semantics
  - enum_values
  - permission_rule
  - cache_ttl
  - cache_invalidation
  - data_retention
  - lifecycle_state
  - backward_compatibility
  - migration_strategy
  - backfill_strategy
  - external_client_support
  - protected_domain_approval
  - transaction_isolation
  - row_lock_strategy
  - rollback_or_compensation
  - idempotency_strategy
```

Decision output:

```yaml
human_decision_needed:
  contract: ""
  missing_decision: ""
  options_if_obvious: []
  risk_of_guessing: ""
  blocked_artifacts: []
```

Do not invent the answer even if one option appears common.

## Drift Detection Checklist

Compare connected graph nodes for:

- Field presence.
- Field name.
- Type.
- Nullability.
- Default value.
- Enum values.
- Length, precision, scale.
- Index and uniqueness.
- Relation and foreign key behavior.
- Validation rule.
- Permission rule.
- Serialization name.
- API request and response shape.
- Error contract.
- Version support.
- Cache key, TTL, structure, and invalidation.
- Test coverage.
- Documentation.
- Protected domain status.
- Migration classification.
- Repair confidence and risk score.

## Recovery Strategy

When verification fails, return a minimal recovery plan:

```yaml
recovery_strategy:
  failed_check: ""
  minimal_fix:
    files: []
    action: ""
  risk: "low | medium | high"
  confidence: 0.0
  requires_human_decision: false
  requires_approval: false
  missing_decision: ""
  can_continue: true
```

Recovery rules:

- Prefer the smallest graph-complete fix.
- Do not expand into unrelated refactors.
- If the fix requires semantic choice, stop at NEED_HUMAN_DECISION.
- If the fix touches a protected domain, stop at NEED_APPROVAL unless explicit approval and adequate verification exist.
- If confidence is below 0.80, require human review.
- If adapter capability is missing, mark not_supported instead of failed.
