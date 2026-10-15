---
type: prd
feature: xpp-agentic-development-test-generation
journey_ref: docs/ux/xpp-agentic-development-journey.md
personas: [xpp-developer]
acceptance_criteria:
  - id: tests-requirement-trace
    statement: "As a xpp-developer, I want tests tied to intended behavior so that the agent's implementation assumptions are not the only source of expected results."
    stage_ref: engineer-and-compile-change
    success_metric: "Each generated scenario names the approved outcome, assertion and applicable boundary/regression behavior; unresolved expected behavior is surfaced before test completion."
    test_status: pending
  - id: tests-systest-artifacts
    statement: "As a xpp-developer, I want valid SysTest artifacts so that code-level business assertions can run on the selected application version."
    stage_ref: prepare-tests-and-delivery
    success_metric: "Supported scenarios produce complete SysTest classes with verified framework/API references and isolation settings; test sources are included in an attributable build or explicitly marked uncompiled."
    test_status: pending
  - id: tests-playwright-artifacts
    statement: "As a xpp-developer, I want executable Playwright TypeScript and a human-readable runbook so that browser scenarios can be executed by a user or capable tool."
    stage_ref: prepare-tests-and-delivery
    success_metric: "Supported UI scenarios include non-placeholder TypeScript, declared runner/configuration and verified navigation/locator assumptions, plus a matching runbook; unverifiable execution prerequisites are explicit blockers."
    test_status: pending
  - id: tests-data-prerequisites
    statement: "As a xpp-developer, I want existing-data selection and missing prerequisites made explicit so that tests do not mutate arbitrary business records."
    stage_ref: prepare-tests-and-delivery
    success_metric: "Each data-dependent scenario specifies environment/company, selection criteria, prerequisites, identifiable test records, approved effects and stop conditions without embedding real credentials."
    test_status: pending
  - id: tests-step-evidence-contract
    statement: "As a xpp-developer, I want assertions and evidence at each creation/save step so that saved outcomes are reproducible."
    stage_ref: prepare-tests-and-delivery
    success_metric: "Every declared creation/save checkpoint has an expected persisted result, screenshot artifact and structured step outcome; an unsaved-form screenshot alone cannot pass the checkpoint."
    test_status: pending
  - id: tests-retention-cleanup
    statement: "As a xpp-developer, I want retained browser records and explicit cleanup guidance so that I can inspect results without silent deletion."
    stage_ref: prepare-tests-and-delivery
    success_metric: "Generated browser scenarios produce created-record identifiers and cleanup instructions, retain tagged records by default, and do not imply rollback can reverse posting or external effects."
    test_status: pending
  - id: tests-performance-verification-plan
    statement: "As a xpp-developer, I want a representative performance-verification plan so that efficiency can be measured rather than asserted from compilation."
    stage_ref: prepare-tests-and-delivery
    success_metric: "Material performance scenarios state data-volume assumptions, measurement method and an agreed threshold or explicit missing budget; no assessed/generated scenario is recorded as a measured pass."
    test_status: pending
  - id: tests-deferred-runtime-delivery
    statement: "As a xpp-developer, I want compiled delivery with runtime checks deferred so that generated artifacts remain useful without false test-success claims."
    stage_ref: prepare-tests-and-delivery
    success_metric: "Deferred deliveries disclose compilation/artifact validation scope and mark SysTest/browser execution not run, pending, or blocked rather than passed."
    test_status: pending
---

# Test Artifact Generation PRD

## Goal - Problem / Solution / Impact

Provide meaningful test artifacts, not just code that resembles tests. Separate expected business behavior from the generated implementation and distinguish test preparation from execution.

Generate both SysTest and executable Playwright TypeScript with a matching human-readable runbook. The developer can deliver compiling changes and generated artifacts while deferring runtime checks.

## Personas (refs)

`xpp-developer`, as defined in the [journey](../../ux/xpp-agentic-development-journey.md).

## High-level user journey

Covers `engineer-and-compile-change` and `prepare-tests-and-delivery`. Test planning can inform the approved implementation before generation finishes.

## Functional requirements

- Trace scenarios to approved functional behavior, not exclusively to the generated code.
- Generate positive, negative, boundary and regression assertions where applicable; explain omitted/blocked coverage.
- Use target-version SysTest patterns, isolation attributes and a suitable approved test model.
- Generate executable Playwright TypeScript and run instructions for the declared runner/UI version; do not invent reliable selectors.
- Parameterize environment/company and nonsecret inputs; authentication is supplied interactively or through an approved external mechanism.
- Discover existing prerequisites through approved read paths and define missing-data creation steps.
- Include checkpoint assertions, screenshots, per-step results, persisted-state checks and created-record manifests.
- Retain tagged browser-created records with explicit cleanup guidance; respect SysTest isolation limits and asynchronous/external side effects.
- Propose meaningful performance measurements when the feature requires them.
- Keep generated, compiled/typechecked, executed and passed statuses distinct.

## Non-functional requirements

Artifacts must be complete for the declared supported scenario, readable, reproducible and free of embedded secrets. If prerequisites/UI locators cannot be verified, disclose the blocker instead of replacing it with a TODO and claiming executable coverage.

Cross-tool handoff must carry run identity and the outcome schema. Universal Playwright-tool/UI compatibility is not promised.

Typechecking generated TypeScript and compiling test X++ are artifact checks, not evidence of UDE execution.

## Success metrics

Evaluate the frontmatter criteria on reviewed scenarios with agreed expected behavior. Track coverage and unverified assumptions separately from runtime pass rates.

## Out of scope

Launching browsers as an unconditional VS-agent requirement, substituting browser screenshots for SysTest, publishing authentication state, creating arbitrary data without approval, and claiming runtime tests passed during generation.

## Dependencies and implementation gates

Confirmed behavior, compatible framework/examples, runner/UI-version policy, approved model placement, and evidence/result contracts. Concrete Playwright selectors and test-data assumptions must be verified for each supported scenario.
