# Execution Protocol

Use this reference at the start and end of every task. It defines how the skill works across different agents with different capabilities.

## Capability Negotiation

Start by declaring the current runtime capability:

```yaml
capabilities:
  can_read_files: true
  can_edit_files: true
  can_read_git_diff: true
  can_execute_commands: false
  can_run_tests: false
  can_execute_sql: false
  can_access_network: false
  can_parse_openapi: true
  can_parse_graphql: true
  can_parse_proto: false
  can_parse_typescript: true
  can_parse_drizzle: true
  can_parse_prisma: false
  can_parse_sql: true
```

Then declare verification mode:

```yaml
verification_mode: "automated | manual | mixed | unsupported"
```

Downgrade rules:

- If commands cannot run, do not claim tests passed; emit a manual verification checklist.
- If files cannot be edited, produce a patch plan instead of saying changes were applied.
- If SQL cannot be executed, inspect SQL statically and mark runtime database checks as manual or unsupported.
- If a parser is unavailable, use conservative text inspection and lower confidence.
- If git diff is unavailable, use changed files from the user or scan relevant artifacts with lower confidence.

## Evidence First

Every confidence value must be backed by evidence. Do not write confidence based on intuition.

Required pattern:

```yaml
confidence:
  value: 0.96
  basis:
    - path: "apps/server/src/routes/user.ts"
      evidence: "Response field display_name is returned."
    - path: "apps/server/src/db/schema/user.ts"
      evidence: "Column display_name exists and is nullable."
```

Invalid pattern:

```text
Confidence: 95%
Reason: Seems correct.
```

Evidence can include:

- File paths and line references.
- Diff hunks.
- Schema declarations.
- API specs.
- Tests.
- Command output.
- Project adapter declarations.
- Explicit user decisions.

If evidence is weak, lower confidence and request review when below threshold.

## Non-goals

Declare non-goals before analysis or repair:

```yaml
non_goals:
  - "No unrelated refactoring"
  - "No formatting-only churn"
  - "No dependency upgrade"
  - "No performance optimization unless required for consistency"
  - "No architecture redesign"
  - "No security redesign"
  - "No protected-domain modification without approval"
```

If the user explicitly asks for one of these, move it from non-goals to goals and re-run impact analysis.

## Rollback Plan

Every repair that changes files, schema, cache, generated artifacts, or configuration needs rollback:

```yaml
rollback_plan:
  files:
    - "apps/server/src/routes/tax.ts"
    - "apps/server/src/db/schema/tax.ts"
  restore_method: "git checkout -- <files> or reverse patch"
  verification:
    - "npm run build"
    - "npm test -- tax"
```

If git is unavailable, use reverse patch or documented manual restore steps. Do not touch production data, live caches, or external services without explicit approval and a rollback strategy.

## Verification Downgrade

When automated verification cannot run, report:

```yaml
verification:
  commands_run: []
  not_run:
    - command: "npm test"
      reason: "Agent cannot execute commands."
  manual_checklist:
    - "Run npm test"
    - "Run migration dry-run"
    - "Confirm OpenAPI generation diff"
```

Unsupported verification is not success.

## Exit Criteria

Evaluate exit criteria before final status:

```yaml
exit_criteria:
  no_known_drift: true
  verification_passed: true
  protected_domain_modified: false
  confidence_above_threshold: true
  remaining_risks_documented: true
  rollback_plan_ready: true
```

Status rules:

- `consistent`: all exit criteria true.
- `partial_with_risk`: work is useful but some non-blocking checks are manual, pending, or lower-confidence, with risks documented.
- `blocked`: a protected-domain approval, behavior-changing decision, adapter capability, authority conflict, failed required verification, or unmet required exit criterion prevents the requested action from safely continuing.

Do not use `blocked` merely because optional verification could not run, confidence is lower than ideal, or a low-risk question remains. Use `partial_with_risk` with a manual checklist and batched follow-up questions.
