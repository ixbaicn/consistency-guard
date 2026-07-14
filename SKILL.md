---
name: consistency-guard
description: Guard cross-layer consistency and risk-aware repair across schemas, migrations, backend models, DTOs, validators, API contracts, SDKs, frontend types, forms, caches, tests, docs, and protected domains. Use when modifying or reviewing data shape, API contracts, business rules, permissions, persistence, generated clients, raw SQL, transactions, financial flows, rollback plans, or any change that may drift across application layers.
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
- Continue with explicit assumptions for low-risk ambiguity; stop only for blocking business decisions, protected-domain writes, destructive changes, or unsafe execution.
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
17. Decision Gate: continue with documented assumptions for low-risk ambiguity; enter NEED_HUMAN_DECISION, NEED_HUMAN_REVIEW, or NEED_APPROVAL only when the missing information blocks safe review or execution.
18. Verification Checklist: produce and complete a checklist for the change type, or a manual checklist when commands cannot run.
19. Exit Criteria: evaluate no known drift, verification status, protected-domain status, confidence threshold, rollback plan, and documented remaining risk.
20. Final Report: return consistent, blocked, or partial_with_risk.

## State Gates

Use state names only when they clarify a blocked or high-risk workflow; do not dump the full state machine in routine answers.

Allowed completion states:

- `consistent`: no known drift remains and exit criteria are evidenced.
- `partial_with_risk`: useful work completed, but manual checks, unsupported verification, or lower-confidence items remain.
- `blocked`: a required decision, approval, authority resolution, adapter capability, or failed verification prevents safe progress on the requested action.

Interrupt states:

- `NEED_HUMAN_DECISION`: missing business or compatibility semantics that would change behavior, data, permissions, migration, cache lifecycle, or external contracts.
- `NEED_HUMAN_REVIEW`: confidence is below threshold or evidence is weak.
- `NEED_APPROVAL`: protected domain, financial flow, destructive migration, transaction, queue, webhook, or security boundary is touched.
- `CAPABILITY_UNSUPPORTED`: the current agent cannot inspect, execute, parse, or verify a required step.
- `SOURCE_OF_TRUTH_AMBIGUOUS` or `CONTRACT_CONFLICT`: authority cannot be resolved from project evidence.
- `VERIFICATION_FAILED`: required automated or manual verification failed.

Do not allow execution without capability negotiation, project profile check, graph-derived impact view, non-goals, protected-domain scan, migration classification, risk score, evidence, and rollback plan when edits are planned. Do not return `consistent` with unresolved checklist items, unsupported verification treated as passed, active drift findings, blocked protected domains, missing rollback plan, unproven exit criteria, or confidence below threshold.

## Interruption Policy

Do not stop immediately for every uncertainty. Prefer one pass of useful work, then batch questions.

Continue and record assumptions when:

- The issue is P2/P3, documentation-only, test-only, naming-only, or low-blast-radius.
- The agent can review impact without choosing business semantics.
- Verification cannot run but a manual checklist is enough for the current request.
- Source-of-truth confidence is weaker than ideal but there is only one plausible candidate and no write will be executed.

Stop or request approval when:

- The next action would modify protected domains, permissions, financial flows, destructive migrations, transactions, locks, queues, webhooks, or production data.
- A missing default, nullability, enum, permission, compatibility, cache TTL, backfill, or rollback decision changes runtime behavior.
- Multiple authority candidates conflict and choosing one would decide product or data semantics.
- The user asked for execution, but verification or rollback requirements for a P0/P1 change are unavailable.

When stopping, ask only the smallest set of questions needed to unblock the next safe step. Do not ask separate questions one at a time when they can be batched.

## Output Protocol

Default to a concise human-readable report unless the user requests YAML/JSON, a tool needs structured output, or the task is a high-risk audit.

Default report:

```text
Status: consistent | blocked | partial_with_risk
Scope: changed contract and non-goals
Source of truth: artifact + evidence
Must fix now: P0/P1 findings with evidence
Can defer: P2/P3 findings with risk
Need decision/approval: missing semantics or protected-domain changes
Assumptions used: low-risk assumptions made to keep work moving
Verification: commands run, not run, failed, or manual checks
Rollback: files and restore method for edits
```

Use the full machine-readable schema in `references/report-format.md` only when structured output is explicitly useful.

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
- `references/report-format.md`: full YAML report schema and compact report guidance for structured-output or high-risk audit tasks.
- `references/adapters-and-extension.md`: language, database, API, frontend, cache, test, documentation adapter capabilities.
- `references/engineer-lenses.md`: distilled review lenses from relevant senior engineering roles.
