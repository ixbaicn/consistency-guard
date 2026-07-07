# Artifact Discovery

Use artifact discovery before source-of-truth discovery. The agent must know what artifacts exist before deciding which artifact is authoritative.

## Default Ignore Rules

Ignore generated, build, vendor, dependency, cache, and coverage outputs unless the user explicitly asks to inspect them:

```yaml
artifact_ignore:
  - ".git/**"
  - "node_modules/**"
  - "vendor/**"
  - "dist/**"
  - "build/**"
  - "coverage/**"
  - "generated/**"
  - ".next/**"
  - ".nuxt/**"
  - "target/**"
  - "bin/**"
  - "obj/**"
  - "__pycache__/**"
```

Generated artifacts, snapshots, initialization SQL, SDKs, documentation, caches, and derived schemas may be useful evidence or outputs, but they are not authoritative by default. Treat them as authority only when project conventions or explicit documentation say they are authoritative.

## Artifact Classes

Classify artifacts by role, not by framework:

```yaml
artifact_classes:
  migration:
    examples: ["migrations/**", "db/migrate/**", "**/*migration*"]
    role: "persistence_change_history"
  database_schema:
    examples: ["schema.sql", "structure.sql", "db/schema.*"]
    role: "actual_or_declared_persistence_shape"
  orm_or_entity:
    examples: ["**/entity/**", "**/model/**", "schema.prisma", "drizzle/schema.*"]
    role: "application_persistence_mapping"
  repository:
    examples: ["**/repository/**", "**/*Repository*", "**/dao/**"]
    role: "data_access_boundary"
  service_or_domain:
    examples: ["**/service/**", "**/domain/**", "**/usecase/**"]
    role: "business_rule_boundary"
  dto_or_transport_type:
    examples: ["**/dto/**", "**/*DTO*", "**/*Request*", "**/*Response*"]
    role: "transport_shape"
  validator:
    examples: ["**/validator/**", "**/*schema*", "**/*rules*"]
    role: "input_and_business_validation"
  api_contract:
    examples: ["openapi.yaml", "openapi.json", "schema.graphql", "proto/**"]
    role: "external_contract"
  sdk_or_client:
    examples: ["**/sdk/**", "**/client/**", "**/api.ts", "**/generated/**"]
    role: "contract_consumer_or_generated_output"
  frontend_type:
    examples: ["**/*.types.ts", "**/types/**", "**/models/**"]
    role: "client_side_shape"
  frontend_surface:
    examples: ["**/forms/**", "**/tables/**", "**/pages/**", "**/components/**"]
    role: "user_visible_contract_use"
  cache_contract:
    examples: ["**/cache/**", "**/*redis*", "**/ttl*", "docs/cache*"]
    role: "cached_shape_and_lifecycle"
  test:
    examples: ["**/*test*", "**/*spec*", "tests/**", "__tests__/**"]
    role: "behavior_and_contract_verification"
  documentation:
    examples: ["README*", "docs/**", "adr/**", "architecture/**"]
    role: "human_contract_record"
```

## Artifact Registry

Build a registry before changing code:

```yaml
artifact_registry:
  - path: ""
    class: ""
    role: ""
    contract_candidates: []
    version: ""
    generated: false
    source_candidate: false
    confidence: 0.0
    adapter: ""
    adapter_capabilities: []
```

Confidence guidance:

- 0.90 to 1.00: strong naming, direct imports, known config, or explicit project declaration.
- 0.70 to 0.89: likely based on patterns and references.
- 0.50 to 0.69: weak signal; require human review before authority decisions.
- Below 0.50: do not rely on this artifact for consistency decisions.

## Contract Priority

When multiple artifacts describe the same contract, resolve conflicts by declared priority. If the project does not declare priority, infer cautiously and report confidence.

## Authoritative Source of Truth

Identify the authoritative source of truth explicitly before deciding drift or making changes.

Authority cannot be assigned merely because an artifact exists, is newer, is easier to parse, or is generated from somewhere else. Multiple artifacts may exist for the same contract.

Do not automatically treat these as authoritative:

```yaml
non_authoritative_by_default:
  - generated_artifacts
  - snapshots
  - initialization_sql
  - sdks
  - documentation
  - caches
  - derived_schemas
```

Use this resolution order:

1. Explicit project documentation or `consistency-contract.yaml`.
2. Build or generation configuration showing which artifact generates which outputs.
3. Repository conventions such as migration-first, schema-first, OpenAPI-first, GraphQL-first, or protobuf-first.
4. Import and generation edges in the Consistency Graph.
5. Human project decision.

If multiple artifacts claim authority and cannot be reconciled using project conventions or explicit documentation, stop:

```yaml
state: "NEED_HUMAN_DECISION"
reason: "Multiple artifacts claim authoritative source-of-truth status and cannot be reconciled."
authority_candidates:
  - path: ""
    claim: ""
    evidence: []
required_project_decision: "Choose the authoritative source of truth for this contract."
```

This is not a soft warning. Do not continue by choosing the most convenient artifact.

Example project declaration:

```yaml
contract_priority:
  User:
    - migration
    - actual_database_schema
    - domain_model
    - backend_dto
    - api_contract
    - sdk_or_client
    - frontend_type
    - documentation
```

Conflict rule:

```text
Higher-priority artifact wins.
Lower-priority artifact is drift.
If priority is absent or confidence is below threshold, enter NEED_HUMAN_DECISION.
```

Common priority patterns:

- Database-first systems: migration, actual schema, entity, DTO, API spec, client.
- Contract-first REST systems: OpenAPI, generated server types, implementation, generated SDK, frontend.
- GraphQL systems: schema, resolvers, domain, generated client types, frontend.
- Event-driven systems: event schema, producer, consumer, storage projection, docs.

Never assume a universal priority order across projects.
