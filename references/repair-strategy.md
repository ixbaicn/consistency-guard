# Repair Strategy

Use this reference when a task involves migration, raw SQL replacement, ORM adoption, automated repair, security-sensitive code, transactions, or a report of remaining issues.

## Repair Queue

Every proposed repair must be represented as a queue item with risk and status:

```yaml
repair_queue:
  - item: "payment service raw SQL migration"
    priority: "P0 | P1 | P2 | P3"
    status: "done | pending | blocked | needs_approval | not_supported"
    risk_level: "critical | high | medium | low"
    confidence: 0.0
    migration_classification: ""
    protected_domain: ""
    reason: ""
    requires: []
    evidence: []
    rollback:
      files: []
      restore_method: ""
      verification: []
```

Priority meanings:

- `P0`: protected domain, security boundary, financial transaction, authentication, authorization, destructive migration, data loss risk, or external contract break.
- `P1`: transactional write, order/inventory state, batch write, idempotency, rollback, compatibility migration, or high-blast-radius API change.
- `P2`: readonly query, aggregation, report, documentation-backed contract sync, low-blast-radius type or DTO repair.
- `P3`: cleanup, naming, docs, tests, low-risk generated output refresh.

Status rules:

- `blocked`: cannot continue without project decision, adapter support, tests, transaction semantics, or approval.
- `needs_approval`: can be understood, but touches protected domain or high-risk operation.
- `not_supported`: current adapters cannot verify safety.
- `pending`: safe enough to plan but not yet executed.
- `done`: repaired and verified.

## Protected Domains

Treat these domains as approval-required zones by default:

```yaml
protected_domains:
  authentication:
    approval_required: true
  authorization:
    approval_required: true
  rbac:
    approval_required: true
  acl:
    approval_required: true
  upload_access:
    approval_required: true
  download_access:
    approval_required: true
  storage:
    approval_required: true
  oss:
    approval_required: true
  cdn:
    approval_required: true
  payment:
    approval_required: true
  settlement:
    approval_required: true
  withdrawal:
    approval_required: true
  wallet:
    approval_required: true
  balance:
    approval_required: true
  coupon:
    approval_required: true
  cdk:
    approval_required: true
  invite:
    approval_required: true
  commission:
    approval_required: true
  order:
    approval_required: true
  inventory:
    approval_required: true
  refund:
    approval_required: true
  webhook:
    approval_required: true
  cron:
    approval_required: true
  mq:
    approval_required: true
  notification:
    approval_required: true
  audit:
    approval_required: true
  rate_limit:
    approval_required: true
  captcha:
    approval_required: true
  websocket_permission:
    approval_required: true
```

Detection signals:

- File path or symbol names contain protected terms.
- Code touches permission, role, ownership, tenant, upload, payment, wallet, refund, settlement, balance, order state, inventory, webhook, cron, queue, audit, rate limit, or captcha behavior.
- Query writes or locks rows in protected tables.
- API changes expose, hide, or mutate protected resources.

Protected-domain rule:

```text
Protected Domain -> NEED_APPROVAL unless the user explicitly approved the scope and the project has adapters/tests sufficient to verify the change.
```

Blocked protected-domain repairs must record why:

```yaml
blocked_reason:
  reason: "Security Boundary | Financial Transaction | Permission Boundary | Transaction Semantics | Unsupported Adapter"
  requires:
    - "transaction isolation"
    - "row lock strategy"
    - "rollback behavior"
    - "compensation behavior"
    - "idempotency key"
    - "authorization model"
```

## Migration Classification

Classify every migration or repair before editing:

```yaml
migration_classification:
  type: "safe_readonly | simple_write | batch_write | transaction | financial_transaction | distributed_transaction | permission_security | ddl | maintenance | cleanup | background_job"
  confidence: 0.0
  auto_approved: false
  approval_required: false
  evidence: []
```

Readonly auto-approval candidates:

- `SELECT`
- `COUNT`
- `SUM`
- `AVG`
- `MIN`
- `MAX`
- `GROUP BY`
- readonly joins without locks

These are only auto-approved when they do not touch protected-domain semantics and do not change permissions, visibility, cache invalidation, or external contracts.

Human approval triggers:

- `INSERT`
- `UPDATE`
- `DELETE`
- `UPSERT`
- `MERGE`
- `TRUNCATE`
- `ALTER`
- `DROP`
- `CREATE INDEX` on large or production-critical tables
- `BEGIN`
- `COMMIT`
- `ROLLBACK`
- `SAVEPOINT`
- `FOR UPDATE`
- locks
- queue publish or consume
- webhook send or verify
- balance, wallet, payment, withdrawal, refund, settlement, order, inventory writes

Transaction classification:

```yaml
transaction_levels:
  read_only:
    default_risk: "low"
  simple_write:
    default_risk: "medium"
  batch_write:
    default_risk: "high"
  financial_transaction:
    default_risk: "critical"
  distributed_transaction:
    default_risk: "critical"
```

If `BEGIN`, `FOR UPDATE`, row locks, idempotency, compensation, or balance mutation is present, do not auto-repair. Enter NEED_APPROVAL or blocked with required transaction details.

## Risk Scoring

Do not report only raw remaining counts. Quantify by priority and category:

```yaml
risk_summary:
  total_findings: 273
  by_priority:
    P0: 19
    P1: 62
    P2: 118
    P3: 74
  by_category:
    security: 12
    transaction: 31
    readonly: 118
    cleanup: 44
    documentation: 68
```

Risk inputs:

- Protected domain involvement.
- Write versus readonly behavior.
- Transaction or lock usage.
- Financial, balance, inventory, or order state impact.
- External API or SDK compatibility.
- Data loss or migration reversibility.
- Test coverage.
- Adapter capability.
- Confidence score.

## Confidence

Every repair and migration classification needs confidence derived from evidence:

```yaml
confidence_policy:
  auto_repair_minimum: 0.90
  safe_readonly_minimum: 0.85
  human_review_below: 0.80
  protected_domain_auto_repair: "never"
```

Evidence-first rule:

```yaml
confidence:
  value: 0.96
  evidence:
    - path: "apps/server/src/routes/tax.ts"
      reason: "Readonly SUM and COUNT aggregation only."
    - path: "apps/server/src/db/schema/tax.ts"
      reason: "Referenced columns exist in schema."
```

If evidence is missing, confidence is invalid and the item requires human review.

Examples:

- Health check readonly query: confidence 1.00, risk low.
- Tax summary aggregation: confidence 0.96, risk low or medium depending on domain.
- Order transaction: confidence 0.42, risk high, human review required.
- Payment migration: confidence 0.18, risk critical, blocked or needs approval.

## Technical Debt Report

Track debt so repeated repair passes become measurable:

```yaml
technical_debt:
  total: 273
  by_kind:
    raw_sql: 198
    mysql2: 273
    missing_tests: 41
    no_migration: 9
    unknown: 25
```

Use technical debt counts as trend metrics, not as risk by themselves. A small P0 count is more important than a large P3 cleanup count.

## Repair Lifecycle

Use this lifecycle for every repair:

```text
Plan -> Execute -> Verify -> Rollback Ready -> Exit Criteria
```

Do not execute without a rollback plan. If the agent cannot execute or verify, emit a manual execution or verification checklist and keep status pending, partial_with_risk, or blocked as appropriate.
