---
type: prd
feature: xpp-agentic-development-authoring-build
journey_ref: docs/ux/xpp-agentic-development-journey.md
personas: [xpp-developer]
acceptance_criteria:
  - id: authoring-approved-plan
    statement: "As a xpp-developer, I want to approve a grounded change plan so that generated objects and dependencies match the required extension."
    stage_ref: decide-reuse-or-extension
    success_metric: "Every mutation plan names behavior, object/type, owned model/package, dependencies, tests and risks; writes outside that approved scope stop for approval."
    test_status: pending
  - id: authoring-backend-ownership
    statement: "As a xpp-developer, I want ownership enforced by the backend so that prompts cannot authorize changes to standard or unowned ISV models."
    stage_ref: engineer-and-compile-change
    success_metric: "Qualification attempts to write outside the explicitly owned custom-model scope are rejected before mutation, including misleading model-name cases."
    test_status: pending
  - id: authoring-capability-matrix
    statement: "As a xpp-developer, I want operation-specific capabilities so that a schema or menu entry is not mistaken for executable support."
    stage_ref: engineer-and-compile-change
    success_metric: "Every requested type/subtype operation is declared supported, manual, unsupported, or unverified with version evidence; unsupported dispatch does not report success."
    test_status: pending
  - id: authoring-exact-placement
    statement: "As a xpp-developer, I want explicit model and project placement so that objects do not silently land in the active model."
    stage_ref: engineer-and-compile-change
    success_metric: "Unknown explicit model targets fail instead of falling back; intended model ownership and exact project references are independently verified."
    test_status: pending
  - id: authoring-safe-metadata-write
    statement: "As a xpp-developer, I want API-based metadata writes with complete read-back so that fields, source, and unrelated children are preserved."
    stage_ref: engineer-and-compile-change
    success_metric: "Supported qualification writes verify intended source and metadata, detect unsupported properties, preserve unrelated collections, and surface partial/failed validation in the tool result."
    test_status: pending
  - id: authoring-model-dependencies
    statement: "As a xpp-developer, I want approved custom model/package creation and dependency changes so that the plan can establish correct placement and references."
    stage_ref: engineer-and-compile-change
    success_metric: "Model/dependency changes occur only when listed in the approved plan and applied through a verified API or a supported manual step whose outcome is checked."
    test_status: pending
  - id: authoring-performance-rationale
    statement: "As a xpp-developer, I want explicit performance reasoning so that compiling code is not mistaken for efficient business logic."
    stage_ref: engineer-and-compile-change
    success_metric: "Each material data/batch path records relevant relations, filters, volume assumptions, indexes, processing and transaction choices, plus unmeasured risks and a proposed verification approach."
    test_status: pending
  - id: authoring-attributable-build
    statement: "As a xpp-developer, I want structured compiler results so that the compiled delivery corresponds to the changes I approved."
    stage_ref: engineer-and-compile-change
    success_metric: "A compiled status requires zero compiler errors, disclosed warnings, recorded toolchain/source scope and logs; stale, failed, unknown, or missing results cannot yield compiled."
    test_status: pending
  - id: authoring-local-only-build
    statement: "As a xpp-developer, I want local compilation isolated from cloud effects so that deployment approval remains meaningful."
    stage_ref: engineer-and-compile-change
    success_metric: "No local-plan/build approval causes an online deployment or synchronization; builds whose effects cannot be safely determined stop rather than bypassing approval."
    test_status: pending
  - id: authoring-repair-reconciliation
    statement: "As a xpp-developer, I want bounded repair and state reconciliation so that failures do not produce duplicate objects or unapproved changes."
    stage_ref: engineer-and-compile-change
    success_metric: "Repairs respect a declared budget and approved scope; partial writes and timed-out mutations are reconciled before retry, with exhausted/unknown outcomes surfaced."
    test_status: pending
---

# Authoring and Compilation PRD

## Goal - Problem / Solution / Impact

Turn a justified customization into a focused, compiling change without sacrificing ownership, behavior, placement, or performance reasoning.

Extend the companion architecture with verified operation capabilities, backend approval/ownership checks, safe metadata application, and attributable compiler diagnostics.

## Personas (refs)

`xpp-developer`, as defined in the [journey](../../ux/xpp-agentic-development-journey.md).

## High-level user journey

Covers `decide-reuse-or-extension` and `engineer-and-compile-change`. Approval follows the evidence-backed discovery decision rather than code being generated first.

## Functional requirements

- Produce and approve an object/model/dependency plan in dependency order.
- Permit new owned custom models/packages and reference changes when included in that plan.
- Use supported Dynamics APIs and JSON/plain-source contracts; do not write raw metadata XML.
- Declare capabilities separately for read/create/update/validate/project operations and concrete subtypes.
- Support genuine metadata extensions; CoC/event classes are not substitutes for table/form field/control extensions.
- Verify exact explicit target placement, complete intended source/metadata, and real project references.
- Preserve unrelated object children; detect stale state and avoid silent replacement of collections.
- Assess relevant query/batch/integration performance concerns using actual target metadata and semantics.
- Compile with the installed X++ toolchain and collect structured errors/warnings and logs.
- Repair within the approved scope and bounded budget; new behavior or dependencies require plan revision.

## Non-functional requirements

Backend checks remain effective when chat-host tool permission is broad. Source version/toolchain/provenance and diagnostics are observable.

Compiler completion is separate from metadata validation. Local build settings cannot perform unapproved cloud effects. Long or unknown mutations have explicit state; no automatic success-shaped fallback.

Performance reasoning is an assessment, not a measured performance claim. Exact compiler/API/timeout support must be probed rather than inferred.

## Success metrics

The frontmatter criteria govern qualification. Track build correctness and warnings against the recorded artifact/source scope. Do not claim a clean build when compilation has not executed.

## Out of scope

Direct base/ISV modification, automatic framework/API guessing, forced raw-XML fallback, runtime execution, production deployment, and unbounded self-repair.

## Dependencies and implementation gates

Discovery-grounding, approved writable-model policy, confirmed reuse rights, compatible Dynamics metadata APIs, and a verified compiler adapter.

The reviewed companion has 19 base create dispatches and nine update implementations; extension creation and model/dependency tooling require additional investigation/implementation or supported manual handoff. These PRD requirements are not claims that the current companion already meets them.
