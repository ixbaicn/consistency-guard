# Project Adapters

Use project adapters to keep Consistency Guard framework-agnostic. The base skill defines protocol; adapters define project-specific facts.

## Project Profile

Load a project profile when available:

```yaml
project_profile:
  name: ""
  stack:
    orm: "drizzle | prisma | typeorm | mybatis | gorm | active_record | none | unknown"
    api: "fastify | express | nestjs | spring | laravel | rails | graphql | grpc | unknown"
    frontend: "react | vue | angular | next | nuxt | none | unknown"
    database: "mysql | postgresql | sqlite | mongodb | redis | unknown"
    migration: "drizzle-kit | prisma | flyway | liquibase | alembic | rails | custom | unknown"
    contract: "openapi | graphql | proto | code-first | schema-first | unknown"
    shared_types: true
```

Suggested locations:

- `.consistency/project-adapter.yaml`
- `docs/consistency-project.yaml`
- `consistency-contract.yaml`
- `projects/<adapter-name>/adapter.yaml`

## Adapter Contract

```yaml
project_adapter:
  name: "fastify-drizzle-react"
  source_of_truth:
    user_contract: "drizzle_schema"
    api_contract: "openapi"
  artifact_map:
    migrations: ["apps/server/drizzle/**"]
    database_schema: ["apps/server/src/db/schema/**"]
    api_contracts: ["apps/server/openapi.yaml"]
    backend_routes: ["apps/server/src/routes/**"]
    frontend_types: ["apps/web/src/types/**"]
    frontend_surfaces: ["apps/web/src/pages/**", "apps/web/src/components/**"]
  generated_files:
    - "apps/web/src/api/generated/**"
  ignore:
    - "dist/**"
    - "coverage/**"
  verification_commands:
    typecheck: "npm run typecheck"
    tests: "npm test"
    build: "npm run build"
    openapi: "npm run openapi:check"
  protected_domains: {}
  rule_overrides: {}
  naming_conventions: {}
```

## Rule Override

Allow explicit project overrides without weakening the base protocol globally:

```yaml
rule_overrides:
  protected_domains:
    payment:
      allow_readonly: true
      approval_required_for_write: true
    audit:
      allow_append_only: true
  confidence_policy:
    safe_readonly_minimum: 0.90
  generated_files:
    sdk_authoritative: false
```

Override rules:

- Overrides must be explicit and loaded from project documentation or user decision.
- Overrides need evidence in the report.
- Overrides cannot silently allow protected writes, destructive migrations, financial transactions, or security boundary changes.
- When override conflicts with base safety rules, base safety wins unless the user gives explicit approval for the current task.

## Adapter Composition

Compose base skill plus one project adapter:

```text
Consistency Guard
+ Project Adapter
-> Source of Truth
-> Artifact Mapping
-> Verification Commands
-> Protected Domains
-> Ignore Files
-> Generated Files
-> Naming Convention
```

Examples:

- `fastify-drizzle-react`
- `nestjs-prisma`
- `nextjs-prisma`
- `laravel-vue`
- `springboot-mybatis`
- `go-gorm`

Do not bake these frameworks into the base skill. Add or load them through adapters.
