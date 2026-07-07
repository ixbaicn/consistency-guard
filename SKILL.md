---
name: consistency-guard
description: Guard full-stack consistency and risk-aware repair across database schemas, migrations, backend models, repositories, services, DTOs, validators, API contracts, SDKs, frontend types, forms, tables, caches, tests, documentation, and protected business domains. Use when modifying or reviewing changes that affect data shape, API contracts, business rules, validation, permissions, persistence, cache structures, compatibility, generated clients, migrations, raw SQL, transactions, security-sensitive domains, financial flows, project adapters, rollback plans, or cross-layer behavior in any language, framework, database, or agent runtime.
---

# Consistency Guard

## Overview

Act as a consistency reviewer and architecture guard, not a code generator. Prevent schema drift, API drift, type drift, database drift, business drift, cache drift, compatibility drift, security drift, performance drift, and documentation drift across full-stack systems.

Use this skill to enforce an AI Consistency Protocol: negotiate current agent capability, load the project profile or adapter, discover artifacts, classify contracts, resolve authority, build a Consistency Graph, derive the impact view, classify repair risk, require evidence, detect protected domains, require human decisions when needed, produce rollback plans, and verify or downgrade verification before completion.

## Core Contract

Require these invariants:

- Identify the changed contract before editing.
- Negotiate current agent capability before promising analysis, edits, commands, tests, SQL execution, parsing, or verification.
- Load project profile, project adapter, rule overrides, and non-goals before impact analysis when they exist.
- Discover and classify project artifacts before choosing a source of truth.
- Identify the authoritative source of truth explicitly; never treat generated artifacts, snapshots, initialization SQL, SDKs, documentation, caches, or derived schemas as authoritative by default.
- Use declared contract priority to resolve conflicts.
- Build a Consistency Graph as the primary model.
- Derive impact trees or checklists from the graph, never from guesses.
- Detect renames separately from delete-plus-create changes.
- Respect declared API, data, SDK, and cache versions.
- Classify migration and repair type before changing persistence, raw SQL, transactions, permissions, or security-sensitive code.
- Treat protected domains as approval-required zones unless the project explicitly declares a safe adapter and approval policy.
- Use risk level, confidence, and reasoned repair status for every proposed repair.
- Derive confidence from cited evidence only; do not report confidence without evidence.
- Provide rollback plan for any file, schema, cache, generated artifact, or config modification.
- State exit criteria before final status and prove each criterion with evidence or mark it unmet.
- Stop for missing business decisions instead of inventing semantics.
- Never claim consistency when verification is skipped, unsupported, or failed.

## Workflow

Follow this sequence for every relevant task:

1. Capability Negotiation: declare what this agent can read, edit, parse, execute, test, inspect, and verify; downgrade unsupported steps.
2. Intake: read the request, diff, changed files, errors, schemas, or specs.
3. Project Profile: load project adapter, artifact map, source priority, verification commands, protected domains, generated files, ignore rules, overrides, and non-goals when available.
4. Artifact Discovery: scan relevant project files and ignore generated/build/vendor output.
5. Artifact Classification: classify migrations, schemas, entities, DTOs, validators, API specs, SDKs, frontend types, forms, cache contracts, tests, and docs.
6. Artifact Registry: record artifact paths, roles, confidence, evidence, and adapter capabilities.
7. Source of Truth Discovery: identify candidate authoritative contracts.
8. Contract Priority Resolution: apply project-declared priority or stop for a human decision.
9. Consistency Graph Build: connect artifacts by contract, mapping, generation, consumption, validation, caching, and documentation relationships.
10. Impact View: derive affected nodes from the changed graph node.
11. Non-goals: state what will not be changed.
12. Protected Domain Detection: identify authentication, authorization, payment, wallet, withdrawal, audit, upload access, websocket permission, rate limit, captcha, and other approval-required domains.
13. Migration Classification: classify changes as safe readonly, simple write, batch write, transactional, financial, distributed, DDL, maintenance, cleanup, background job, or permission/security related.
14. Drift Detection: compare connected nodes and contract groups for structural, semantic, compatibility, security, and documentation drift.
15. Risk Scoring: quantify findings and repairs by priority, category, confidence, blast radius, and reversibility.
16. Repair Plan: include execution, verification, rollback, evidence, confidence, and blocked reasons.
17. Decision Gate: enter NEED_HUMAN_DECISION, NEED_HUMAN_REVIEW, or NEED_APPROVAL when information, confidence, capability, or protected-domain authorization is insufficient.
18. Verification Checklist: produce and complete a checklist for the change type, or a manual checklist when commands cannot run.
19. Exit Criteria: evaluate no known drift, verification status, protected-domain status, confidence threshold, rollback plan, and documented remaining risk.
20. Final Report: return consistent, blocked, or partial_with_risk.

## State Machine

Use these states:

```text
IDLE
CAPABILITY_NEGOTIATION
INTAKE
PROJECT_PROFILE_LOADED
ARTIFACT_DISCOVERY
ARTIFACT_CLASSIFICATION
ARTIFACT_REGISTRY_BUILT
SOURCE_OF_TRUTH_DISCOVERY
CONTRACT_PRIORITY_RESOLUTION
CONSISTENCY_GRAPH_BUILT
CHANGE_IMPACT_VIEW_BUILT
NON_GOALS_DECLARED
PROTECTED_DOMAIN_SCAN_COMPLETE
MIGRATION_CLASSIFIED
DRIFT_SCAN_COMPLETE
RISK_SCORED
REPAIR_PLAN_CREATED
CHECKLIST_CREATED
EXECUTION_ALLOWED
VERIFICATION_RUNNING
EXIT_CRITERIA_CHECKED
RECOVERY_IF_NEEDED
CONSISTENT_DONE
```

Interrupt states:

```text
NEED_HUMAN_DECISION
NEED_HUMAN_REVIEW
NEED_APPROVAL
CAPABILITY_UNSUPPORTED
PROJECT_ADAPTER_MISSING
SOURCE_OF_TRUTH_AMBIGUOUS
CONTRACT_PRIORITY_MISSING
CONTRACT_CONFLICT
ADAPTER_CAPABILITY_MISSING
VERIFICATION_FAILED
```

Do not move to EXECUTION_ALLOWED without capability negotiation, project profile check, graph-derived impact view, non-goals, protected-domain scan, migration classification, risk score, evidence, and rollback plan when edits are planned. Do not move to CONSISTENT_DONE with unresolved checklist items, unsupported verification treated as passed, active drift findings, blocked protected domains, missing rollback plan, unproven exit criteria, or confidence below threshold.

## Output Protocol

Return this structure when reviewing or executing consistency-sensitive work:

```yaml
consistency_report:
  final_status: "consistent | blocked | partial_with_risk"
  capabilities:
    can_read_files: true
    can_edit_files: true
    can_read_git_diff: false
    can_execute_commands: false
    can_run_tests: false
    can_execute_sql: false
    parsers: []
    verification_mode: "automated | manual | mixed | unsupported"
  project_profile:
    adapter: ""
    orm: ""
    api: ""
    frontend: ""
    database: ""
    migration: ""
    contract: ""
    shared_types: false
    rule_overrides: {}
  non_goals: []
  artifact_registry_summary: []
  source_of_truth:
    artifact: ""
    confidence: 0.0
    evidence: []
    priority_basis: ""
  consistency_graph:
    changed_nodes: []
    affected_nodes: []
  drift_findings:
    - category: "schema | api | type | semantic | business | cache | performance | compatibility | security | documentation"
      severity: "blocker | high | medium | low"
      confidence: 0.0
      artifact: ""
      issue: ""
      evidence: []
      required_action: ""
  risk_summary:
    total_findings: 0
    by_priority:
      P0: 0
      P1: 0
      P2: 0
      P3: 0
    by_category: {}
  repair_queue:
    - item: ""
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
  human_decisions_needed: []
  checklist:
    - item: ""
      status: "done | pending | not_applicable | blocked"
  verification:
    commands_run: []
    passed: []
    failed: []
    not_run: []
    not_supported: []
    manual_checklist: []
  recovery_strategy:
    minimal_fix: []
    risk: "low | medium | high"
    confidence: 0.0
    evidence: []
    can_continue: true
  rollback_plan:
    files: []
    restore_method: ""
    verification: []
  exit_criteria:
    no_known_drift: false
    verification_passed: false
    protected_domain_modified: false
    confidence_above_threshold: false
    remaining_risks_documented: false
    rollback_plan_ready: false
  technical_debt:
    by_kind: {}
    total: 0
```

## Hard Boundaries

Do not:

- Invent business requirements, default values, nullability, enum values, permissions, cache TTLs, retention rules, or lifecycle semantics.
- Assume generated artifacts, snapshots, initialization SQL, SDKs, documentation, caches, or derived schemas are authoritative unless project conventions or explicit documentation say so.
- Reconcile competing authority claims by preference or convenience; if project conventions and documentation cannot resolve them, stop and request a project decision.
- Modify protected domains without explicit approval and a stated reason.
- Auto-approve writes, transactions, locks, financial flows, permission changes, webhooks, jobs, or message queues as safe migrations.
- Report only a raw count of remaining findings; always break remaining risk down by priority and category.
- Report confidence without concrete evidence.
- Treat an unavailable capability as a passed check; downgrade to manual verification or mark unsupported.
- Expand scope beyond declared non-goals.
- Make changes without rollback plan.
- Treat a rename as a delete-plus-create without compatibility analysis.
- Treat intentional v1/v2 differences as drift when a compatibility matrix exists.
- Update only one layer when the graph shows connected required changes.
- Modify unrelated files outside the impact scope.
- Claim verification success for adapter capabilities that are unknown or unsupported.
- Use documentation updates as a substitute for contract or implementation updates.

## References

Read these files as needed:

- `references/artifact-discovery.md`: artifact discovery, classification, registry, ignore rules, and contract priority.
- `references/consistency-graph.md`: graph model, relationships, impact view, rename detection, version compatibility, and confidence gates.
- `references/execution-protocol.md`: capability negotiation, evidence-first rules, non-goals, rollback plans, verification downgrade, and exit criteria.
- `references/drift-rules.md`: drift categories, severity rules, human decision points, and recovery strategy.
- `references/repair-strategy.md`: protected domains, migration classification, risk scoring, repair queue, confidence, and technical debt reporting.
- `references/project-adapters.md`: project profile, project adapter contract, rule overrides, verification commands, source priority, generated files, and adapter composition.
- `references/verification-checklists.md`: checklist templates for database, API, type, business, cache, documentation, and compatibility changes.
- `references/adapters-and-extension.md`: language, database, API, frontend, cache, test, documentation adapter capabilities.
- `references/engineer-lenses.md`: distilled review lenses from relevant senior engineering roles.
