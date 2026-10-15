---
type: prd
feature: xpp-agentic-development-ude-verification
journey_ref: docs/ux/xpp-agentic-development-journey.md
personas: [xpp-developer]
acceptance_criteria:
  - id: ude-optional-execution
    statement: "As a xpp-developer, I want to defer runtime verification so that delivery does not force cloud effects."
    stage_ref: prepare-tests-and-delivery
    success_metric: "Declining/deferring runtime execution makes no UDE writes and retains the compiled/generated-artifacts delivery with runtime status not run."
    test_status: pending
  - id: ude-target-bound-approval
    statement: "As a xpp-developer, I want deployment approval bound to the exact target and build so that changed context cannot reuse a stale approval."
    stage_ref: verify-in-approved-ude
    success_metric: "Deployment/sync executes only for the approved environment, artifact/package set and sync choice; changed target/build/scope invalidates approval before effects."
    test_status: pending
  - id: ude-deployment-before-tests
    statement: "As a xpp-developer, I want cloud/local build identity checked so that runtime tests exercise the intended binaries."
    stage_ref: verify-in-approved-ude
    success_metric: "Before tests of changed code, successful deployment evidence for the intended artifact and required synchronization is recorded; mismatched, pending, failed, or unknown deployment blocks execution."
    test_status: pending
  - id: ude-effect-bound-test-approval
    statement: "As a xpp-developer, I want data effects approved separately so that existing-record changes, posting and external/batch effects are intentional."
    stage_ref: verify-in-approved-ude
    success_metric: "Data-writing scenarios require environment/company/scope approval, with separate scenario-specific approval for existing-record changes, posting, batch or external effects; target/record mismatch stops the run."
    test_status: pending
  - id: ude-manual-supported-handoff
    statement: "As a xpp-developer, I want a supported manual step when automation is unavailable so that the agent remains useful without inventing an API."
    stage_ref: verify-in-approved-ude
    success_metric: "Unverified/unsupported adapters yield the specific supported VS action and pending status; execution continues only after attributable logs/status are supplied and checked."
    test_status: pending
  - id: ude-real-systest-results
    statement: "As a xpp-developer, I want actual SysTest results so that discovered or generated tests are not counted as executed."
    stage_ref: verify-in-approved-ude
    success_metric: "The run records selected/discovered/executed counts and per-test outcomes with build/target identity; missing, skipped, failed or unknown outcomes are not passed."
    test_status: pending
  - id: ude-browser-results
    statement: "As a xpp-developer, I want attributable browser outcomes so that an external tool's evidence can be checked against the intended scenario."
    stage_ref: verify-in-approved-ude
    success_metric: "Imported/executed results bind scenario/run/build/environment/company, expected assertions, creation/save screenshots and record identifiers; mismatched or incomplete evidence cannot yield verified."
    test_status: pending
  - id: ude-unknown-outcome-recovery
    statement: "As a xpp-developer, I want unknown cloud outcomes reconciled so that retries do not duplicate side effects or imply rollback."
    stage_ref: verify-in-approved-ude
    success_metric: "Timeout/failure reports retain operation identity and logs; unknown mutations are checked before retry, and no automatic reversal of sync, posting or external effects is claimed."
    test_status: pending
---

# Approved UDE Verification PRD

## Goal - Problem / Solution / Impact

Help the developer verify a change against the connected cloud runtime without allowing compilation, broad chat permission, or a stale approval to trigger unintended business effects.

Support verified adapters and guided manual actions. Runtime verification is optional for an individual delivery but requires actual results when claimed.

## Personas (refs)

`xpp-developer`, as defined in the [journey](../../ux/xpp-agentic-development-journey.md).

## High-level user journey

Covers `prepare-tests-and-delivery` and `verify-in-approved-ude`.

## Functional requirements

- Allow runtime testing to be deferred without performing deployment/sync/data writes.
- Bind deployment approval to the environment, exact built artifacts/packages and sync choice.
- Check deployment completion and required synchronization before exercising changed binaries.
- Obtain separate data-writing test approval, including named company and effect scope.
- Require scenario-specific approval for changes to existing records, posting, batch effects and external calls.
- Run supported SysTest selections and report actual discovery/execution/outcome counts.
- Execute or hand off browser tests and validate returned evidence against the declared scenario/run identity.
- Download/collect attributable deployment/sync/test logs without leaking credentials or restricted content.
- Expose supported manual VS actions where automation is unavailable and keep state pending until checked evidence arrives.
- Reconcile unknown outcomes; never blindly repeat a timed-out mutation or imply database rollback from source restoration.

## Non-functional requirements

Approvals are backend-enforced, explicit, auditable and invalidated by relevant target/build/scope changes. The chosen authentication flow must be supported by the installed tool version and approved policy.

Long-running operations have attributable IDs and observable status. Cancellation is distinguished from a confirmed absence of effects.

Runtime performance claims require declared workload/measurement evidence. Browser/account roles are recorded; an administrator run is not proof of end-user permissions.

## Success metrics

The frontmatter criteria govern runtime qualification on a real approved UDE. Manual completion can satisfy a runtime step when evidence is checked, but is never reported as automated execution.

## Out of scope

Production deployment/promotion, UDE provisioning, guessed private automation APIs, implicit UI automation, automatic reversal of posted transactions, and runtime-success claims based on generated artifacts.

## Dependencies and implementation gates

Compatible build/artifact identity, target/environment/company policy, authentication, deployment/sync/SysTest capability probes, generated test contracts and evidence handling.

Microsoft documents UDE execution through the cloud and supported VS actions. An automation adapter must be demonstrated; its existence is not inferred from that documentation.
