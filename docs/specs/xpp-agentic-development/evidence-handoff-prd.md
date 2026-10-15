---
type: prd
feature: xpp-agentic-development-evidence-handoff
journey_ref: docs/ux/xpp-agentic-development-journey.md
personas: [xpp-developer]
acceptance_criteria:
  - id: evidence-status-dimensions
    statement: "As a xpp-developer, I want separate artifact and runtime statuses so that delivered work does not hide deferred verification."
    stage_ref: prepare-tests-and-delivery
    success_metric: "Each handoff distinguishes source/metadata/build status, generated test artifacts, deployment/sync, SysTest/browser execution, performance assessment/measurement and publication status."
    test_status: pending
  - id: evidence-step-manifest
    statement: "As a xpp-developer, I want structured run/step manifests so that screenshots and outputs are attributable."
    stage_ref: verify-in-approved-ude
    success_metric: "Every creation/save step result references its run/scenario/step, target/company, expected/observed outcome, status, screenshot and created identifiers; incomplete results remain incomplete."
    test_status: pending
  - id: evidence-retained-records
    statement: "As a xpp-developer, I want retained-record and cleanup details so that test data can be inspected and responsibly removed later."
    stage_ref: preserve-evidence-and-handoff
    success_metric: "Each run that creates persistent browser records includes a tagged identifiers manifest, ownership/company, effects and explicit cleanup instructions or an acknowledged non-reversible effect."
    test_status: pending
  - id: evidence-source-control-bundle
    statement: "As a xpp-developer, I want code, tests, documents, screenshots and sanitized results versioned together so that the handoff can be reproduced."
    stage_ref: preserve-evidence-and-handoff
    success_metric: "Approved bundles are committed on a task branch with artifact/source/build/run relationships; permitted screenshots/results are not silently discarded or made permanently git-ignored."
    test_status: pending
  - id: evidence-policy-sanitation
    statement: "As a xpp-developer, I want publication sanitation enforced so that automatic commits do not expose secrets or restricted business/source data."
    stage_ref: preserve-evidence-and-handoff
    success_metric: "Every bundle has a satisfied target-specific publication policy; credentials/auth state are always excluded, and an unknown/failed sanitation check blocks the affected artifact's commit."
    test_status: pending
  - id: evidence-separate-push-approval
    statement: "As a xpp-developer, I want remote publication approved separately so that local commits do not imply permission to push, open a PR or merge."
    stage_ref: preserve-evidence-and-handoff
    success_metric: "Push/PR requires explicit authorization for the intended remote/branch; no task operation writes directly to a shared default branch or auto-merges."
    test_status: pending
  - id: evidence-resumable-context
    statement: "As a xpp-developer, I want a resumable verified-state handoff so that interruptions do not require repeating discovery or unsafe actions."
    stage_ref: preserve-evidence-and-handoff
    success_metric: "Resume records last verified state, source/build/target identities, pending action and approvals; relevant changed context is rechecked before effects and unknown operations are not replayed blindly."
    test_status: pending
  - id: evidence-no-code-handoff
    statement: "As a xpp-developer, I want evidence-backed existing-functionality guidance preserved so that a no-code result is a useful delivery."
    stage_ref: preserve-evidence-and-handoff
    success_metric: "No-code handoffs retain the business outcome, applicable references/guidance, uncertainty and any separately required configuration action without fabricating build/test/deploy results."
    test_status: pending
---

# Evidence and Handoff PRD

## Goal - Problem / Solution / Impact

Make delivery reproducible and honest, including the successful no-code path and runtime-deferred path.

Preserve code, tests, documents, screenshots and permitted sanitized results together while enforcing repository/data policy and separating local commits from remote publication.

## Personas (refs)

`xpp-developer`, as defined in the [journey](../../ux/xpp-agentic-development-journey.md).

## High-level user journey

Covers `prepare-tests-and-delivery`, `verify-in-approved-ude` and `preserve-evidence-and-handoff`. Evidence capture starts earlier; runtime execution is not required for a useful artifact handoff.

## Functional requirements

- Persist the outcome/reuse decision, applicable source/version provenance, plan revisions and approved scope.
- Report generated, metadata-checked, compiled/typechecked, deployed, executed and verified states separately.
- Preserve creation/save screenshots and structured outcomes with run/scenario/step identity.
- Record retained browser test records and cleanup obligations.
- Source-control the permitted complete bundle on a task branch; do not broadly ignore the evidence the user explicitly wants committed.
- Apply target-specific sanitation/redaction/allowance checks before automatic evidence commits.
- Never commit tokens, passwords, cookies, reusable browser authentication state, or unapproved private-source/customer data.
- Block publication of artifacts whose policy compliance cannot be established; report omissions and rationale.
- Require explicit remote push/PR approval and never auto-merge or directly write to a shared default branch.
- Support resumed work through checked source/target identities and last verified state, not silent assumptions.

## Non-functional requirements

Artifact identifiers and references are stable and machine-readable. Missing external evidence remains missing.

Public-repository policy is distinct from internal model/source access. Permission to inspect msdyneng source never implies permission to include its content in a public commit.

Evidence may be sanitized or restricted when necessary, but screenshots/results are not silently dropped from the intended bundle. Size, image-redaction technology, retention and repository storage strategy remain implementation decisions under the approved policy.

## Success metrics

Use the frontmatter criteria on no-code, runtime-deferred, runtime-executed, interrupted, incomplete-evidence and denied-publication cases.

## Out of scope

Credential storage, unauthorized public publication, automatic deletion of retained records, automatic merge, and turning a prepared artifact into a false runtime pass.

## Dependencies and implementation gates

Approved repository/data policy, run/step/status contracts, an artifact manifest, sanitation implementation, and a verified source-control adapter. Runtime adapters are needed only for evidence from executed cloud steps.
