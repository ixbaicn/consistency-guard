# Adapters and Extension

This skill is framework, language, and database agnostic. Use adapters to describe what can be discovered and verified in a project.

## Adapter Model

```yaml
adapter:
  id: ""
  artifact_classes: []
  supports:
    field_presence: true
    type: true
    nullable: true
    default: true
    enum: true
    index: true
    unique: true
    relation: true
    rename_detection: "true | partial | false"
    versioning: "true | partial | false"
    cache_ttl: "true | partial | false"
    generation_detection: "true | partial | false"
    protected_domain_detection: "true | partial | false"
    migration_classification: "true | partial | false"
    transaction_detection: "true | partial | false"
    lock_detection: "true | partial | false"
    risk_scoring: "true | partial | false"
    rollback_planning: "true | partial | false"
    evidence_collection: "true | partial | false"
```

Unknown support means not_supported, not passed.

## Language Adapter Responsibilities

Detect:

- Type definitions.
- DTOs and transport types.
- Enum definitions.
- Optional and nullable semantics when represented.
- Serialization names.
- Validator schemas.
- Generated files.

Do not assume two languages encode nullability or optionality the same way.

## Database Adapter Responsibilities

Detect:

- Tables, collections, or entities.
- Columns or fields.
- Type, nullability, default, length, precision, scale.
- Index, unique, foreign key, relation.
- Migration order and operation type.
- Rename support when explicit.
- Readonly versus write query classification.
- Transaction, lock, and destructive DDL detection.

Database-specific limitations must be reported. Example: if an engine lacks native enum support, enum consistency may live in application validators or check constraints.

## API Adapter Responsibilities

Detect:

- REST route request and response shapes.
- OpenAPI request, response, schema, nullable, enum, and deprecation.
- GraphQL schema, resolver connection, and generated client types.
- Protobuf messages, field numbers, compatibility, and generated code.
- Version boundaries.
- Permission or security-sensitive route boundaries when discoverable.

## Frontend Adapter Responsibilities

Detect:

- API clients.
- Shared types.
- Forms.
- Tables.
- Filters and search.
- State stores.
- User-visible labels.

Frontend labels may help rename detection, but labels are not authoritative business semantics.

## Cache Adapter Responsibilities

Detect:

- Key naming.
- TTL.
- Hash or JSON structure.
- Version suffixes.
- Invalidation paths.
- Read-through and write-through behavior.
- Protected cache keys or tenant/user isolation risks.

## Repair Adapter Responsibilities

Detect when possible:

- Protected domain names and paths.
- Safe readonly operations.
- Writes, batch writes, DDL, transactions, locks, queues, webhooks, jobs, and financial flows.
- Risk priority and confidence inputs.
- Required approval or unsupported verification.

If the adapter cannot distinguish readonly from write behavior, do not auto-repair persistence code.

Missing cache contract documentation should be reported as documentation or cache drift when cache behavior is affected.

## Project Extension File

Projects may define a local contract map or load a project adapter:

```yaml
consistency_contract_version: "1.0.0"
project_profile:
  orm: ""
  api: ""
  frontend: ""
  database: ""
  migration: ""
  contract: ""
  shared_types: false
artifact_ignore: []
artifact_map:
  database_schema:
    patterns: []
  migrations:
    patterns: []
  api_contracts:
    patterns: []
  frontend_types:
    patterns: []
contract_priority: {}
compatibility_matrix: {}
adapters: {}
rule_overrides: {}
confidence_policy:
  source_of_truth_minimum: 0.80
  rename_minimum: 0.70
  drift_minimum_for_auto_fix: 0.75
  auto_repair_minimum: 0.90
  safe_readonly_minimum: 0.85
  human_review_below: 0.80
protected_domains: {}
verification_commands: {}
generated_files: []
non_goals: []
```

Suggested filenames:

- `consistency-contract.yaml`
- `docs/consistency-contract.yaml`
- `.consistency/contract.yaml`
- `.consistency/project-adapter.yaml`

If none exists, infer with confidence and ask for human decisions when authority is unclear.
