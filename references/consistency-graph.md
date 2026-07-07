# Consistency Graph

Use the Consistency Graph as the primary model. Impact trees are derived views for a specific change.

## Node Model

```yaml
node:
  id: ""
  artifact_path: ""
  artifact_class: ""
  contract: ""
  version: ""
  fields: []
  rules: []
  generated: false
  authoritative: false
  confidence: 0.0
```

## Edge Model

```yaml
edge:
  from: ""
  to: ""
  relation: "defines | maps_to | validates | exposes | consumes | generates | caches | invalidates | tests | documents | authorizes"
  contract: ""
  version: ""
  required_consistency: true
  confidence: 0.0
```

## Common Relations

- `defines`: schema, spec, or source artifact defines a contract.
- `maps_to`: entity maps to DTO, DTO maps to API response, API maps to frontend type.
- `validates`: validator enforces field or business rules.
- `exposes`: controller, route, resolver, or handler exposes a contract.
- `consumes`: frontend, SDK, job, or service consumes a contract.
- `generates`: source spec generates SDK, types, docs, or clients.
- `caches`: cache stores a shaped subset or projection.
- `invalidates`: write path clears or updates cache keys.
- `tests`: test verifies structural or behavioral contract.
- `documents`: docs describe the contract.
- `authorizes`: permission logic enforces access to fields or actions.

## Impact View

For a changed node:

1. Include direct graph neighbors.
2. Include transitive neighbors through required-consistency edges.
3. Include tests and docs that mention the same contract.
4. Include cache nodes when stored shape, key, TTL, or invalidation may change.
5. Include versioned nodes only when compatibility matrix says they are affected.

Output:

```yaml
impact_view:
  root_change: ""
  changed_nodes: []
  required_updates: []
  review_only: []
  not_affected: []
  unknown_requires_review: []
```

## Rename Detection

Detect rename before treating a removed field and added field as separate changes.

Signals:

- Similar type, nullability, enum, length, validation, or comments.
- Same UI label or documentation meaning.
- References move from old name to new name in nearby files.
- Migration contains rename operation or backfill from old field to new field.
- API compatibility maps old field to new field.

Output:

```yaml
rename_detection:
  from: ""
  to: ""
  confidence: 0.0
  evidence: []
  compatibility_required: true
  possible_strategies:
    - "breaking_change"
    - "compatible_alias"
    - "dual_read"
    - "dual_write"
    - "migration_backfill"
    - "deprecation_window"
```

Rules:

- Confidence below 0.70 requires NEED_HUMAN_REVIEW.
- Cross-boundary rename requires a compatibility strategy.
- Database, API, cache, and SDK renames are breaking unless compatibility is declared.

## Version Compatibility

Allow intentional version divergence when declared:

```yaml
compatibility_matrix:
  contract: "UserAPI"
  versions:
    v1:
      status: "supported | deprecated | removed"
      fields: []
    v2:
      status: "current"
      fields: []
  mappings:
    - from_version: "v1"
      to_version: "v2"
      field_map:
        nickname: "display_name"
      strategy: "alias_read | transform | unsupported"
```

Rules:

- Declared v1/v2 differences are not drift.
- Undeclared differences are compatibility drift.
- Removed versions need a deprecation or removal policy.
- SDK and frontend consumers must map to supported versions explicitly.

## Confidence Gates

Use confidence to decide whether to proceed:

```yaml
confidence_policy:
  source_of_truth_minimum: 0.80
  rename_minimum: 0.70
  drift_minimum_for_auto_fix: 0.75
  graph_edge_minimum: 0.65
```

Below threshold, enter NEED_HUMAN_REVIEW or NEED_HUMAN_DECISION. Do not silently continue.
