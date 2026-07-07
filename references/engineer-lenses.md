# Engineer Lenses

Use these distilled senior engineering lenses during review. They are responsibilities, not personas.

## Database Architect Lens

Check persistence correctness:

- Migration order and reversibility.
- Nullability, defaults, backfills, and data retention.
- Indexes, uniqueness, relations, and constraints.
- Actual schema versus declared schema.
- Compatibility of data migrations with existing rows.

## API Contract Engineer Lens

Check external contract stability:

- Request and response shape.
- Error format and status codes.
- OpenAPI, GraphQL, protobuf, or RPC spec.
- Versioning, deprecation, and SDK generation.
- Backward compatibility for external clients.

## Backend Domain Engineer Lens

Check business consistency:

- Service and domain rule implementation.
- Validator alignment.
- Permission and authorization boundaries.
- Background jobs, events, and side effects.
- Mapping between persistence and transport models.

## Frontend Type Engineer Lens

Check client contract use:

- Generated or hand-written API types.
- Forms, tables, filters, and state stores.
- User-visible labels and rename implications.
- Client-side validation versus server validation.
- Version-specific API consumers.

## Cache Engineer Lens

Check cached contract correctness:

- Key structure.
- TTL and retention.
- Stored JSON or hash shape.
- Invalidation on writes and renames.
- Versioned cache migration.

## Test Engineer Lens

Check proof of consistency:

- Unit tests for mapping and validation.
- Integration tests for persistence and API.
- Contract tests for API or SDK.
- Migration tests when available.
- Regression tests for compatibility paths.

## Documentation Maintainer Lens

Check human-facing truth:

- README examples.
- Swagger or OpenAPI output.
- Architecture docs.
- Cache contract docs.
- Migration, deprecation, and rollout notes.

## Security Engineer Lens

Check access and exposure:

- Sensitive field visibility.
- Tenant and ownership boundaries.
- Authorization at API and service layers.
- Validation bypass.
- Cache leakage across users or tenants.

Security drift is blocker severity unless proven otherwise.
