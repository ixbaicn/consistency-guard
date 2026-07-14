# Report Format

Use this reference when the user requests YAML/JSON, a downstream tool needs structured output, or the task is a high-risk audit that benefits from a complete machine-readable report.

## Default Human Report

Use this compact format for routine user-facing answers:

```text
Status: consistent | blocked | partial_with_risk
Scope: changed contract and non-goals
Source of truth: artifact + evidence
Must fix now: P0/P1 findings with evidence
Can defer: P2/P3 findings with risk
Need decision/approval: missing semantics or protected-domain changes
Assumptions used: provisional low-risk assumptions made to keep work moving
Verification: commands run, not run, failed, or manual checks
Rollback: files and restore method for edits
```

## Full Structured Report

Return this YAML structure only when structured output is explicitly useful:

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
  assumptions_used:
    - assumption: ""
      scope: ""
      risk: "low | medium | high"
      can_continue: true
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
