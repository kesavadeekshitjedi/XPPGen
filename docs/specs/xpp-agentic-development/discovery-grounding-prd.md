---
type: prd
feature: xpp-agentic-development-discovery-grounding
journey_ref: docs/ux/xpp-agentic-development-journey.md
personas: [xpp-developer]
acceptance_criteria:
  - id: discovery-confirm-outcome
    statement: "As a xpp-developer, I want an agreed business outcome before solution selection so that a proposed extension is not mistaken for the requirement."
    stage_ref: frame-business-outcome
    success_metric: "Every qualification request records current versus desired behavior, relevant business context, constraints, and unresolved questions before a write plan is approved."
    test_status: pending
  - id: discovery-read-only-preflight
    statement: "As a xpp-developer, I want a read-only version/model/environment preflight so that I know which capabilities and targets are actually available."
    stage_ref: frame-business-outcome
    success_metric: "Preflight records available versions, configuration/model identities, permissions and capability failures with zero metadata, business-data, or environment-configuration writes."
    test_status: pending
  - id: discovery-online-build-settings
    statement: "As a xpp-developer, I want automatic online build effects detected so that an approved local build cannot silently deploy or synchronize."
    stage_ref: frame-business-outcome
    success_metric: "Every supported build entry point is classified as local-only, cloud-effect-capable, or unknown; cloud-capable/unknown execution cannot bypass the UDE approval boundary."
    test_status: pending
  - id: discovery-authorized-provenance
    statement: "As a xpp-developer, I want source retrieval to carry authorization and version provenance so that examples are safe and applicable."
    stage_ref: decide-reuse-or-extension
    success_metric: "Each retrieved source/example has a source locator, revision/build relationship and authorization classification; restricted or unverified content is not sent to an unapproved model or publication target."
    test_status: pending
  - id: discovery-existing-capability
    statement: "As a xpp-developer, I want an evidence-backed assessment of existing functionality so that I avoid redundant customization."
    stage_ref: decide-reuse-or-extension
    success_metric: "Each decision identifies applicable existing capability/configuration or explains the needed extension with evidence, searched scope and uncertainty; an empty search alone never proves absence."
    test_status: pending
  - id: discovery-extension-boundary
    statement: "As a xpp-developer, I want a verified extension boundary so that a change uses supported target-version APIs."
    stage_ref: decide-reuse-or-extension
    success_metric: "Each proposed extension names the target object, relevant signature/metadata, supported mechanism and dependencies, or explicitly blocks generation where applicability cannot be established."
    test_status: pending
  - id: discovery-bounded-retrieval
    statement: "As a xpp-developer, I want focused metadata and reference retrieval so that useful context is not replaced by unbounded source dumps."
    stage_ref: decide-reuse-or-extension
    success_metric: "Queries expose their type/model scope, result limit and truncation; follow-up retrieval is attributable to the request and cannot silently treat truncated results as complete."
    test_status: pending
  - id: discovery-no-code-outcome
    statement: "As a xpp-developer, I want a usable no-code result when existing functionality is enough so that the agent does not generate unnecessary objects."
    stage_ref: decide-reuse-or-extension
    success_metric: "No-code qualification cases produce referenced usage/configuration guidance and no code/model/data writes; any requested configuration mutation follows its own approval path."
    test_status: pending
---

# Discovery and Source Grounding PRD

## Goal - Problem / Solution / Impact

Help the developer answer the difficult question first: does the desired business capability already exist, or is an extension necessary?

Combine business clarification and read-only preflight with bounded retrieval of applicable metadata, code, references, and tests. The outcome is a traceable reuse/configuration/extension decision, not an automatic request to generate code.

## Personas (refs)

`xpp-developer`: intermediate, daily Windows/VS2022 development with review time.

## High-level user journey

Owns `frame-business-outcome` and `decide-reuse-or-extension` in the [journey](../../ux/xpp-agentic-development-journey.md).

## Functional requirements

- Capture the business problem, desired observable behavior, examples, constraints, and uncertain assumptions.
- Identify the supported installation/configuration, selected models, and known target context without changing them.
- Detect build settings capable of online deployment/synchronization before executing a purported local build.
- Prefer metadata discovery/read/reference tools over raw metadata file inspection.
- Support approved source/test retrieval with locators and revision/version applicability, including FinOps engineering source when authorized.
- Follow actual relations and reference graphs; distinguish stale/unavailable cross-reference results from a proven absence.
- Compare existing capability, supported configuration, reuse, and extension alternatives; explain the smallest justified change.
- Make engineering-branch versus installed-version conflicts explicit and defer to the target's supported APIs.
- Return useful guidance when no customization is needed; hand any proposed configuration effects to the same approval model used elsewhere.

## Non-functional requirements

Retrieval must be bounded, cancellable where the underlying adapter supports it, and explicit about truncation/failures. No broad source export or model training is required.

Source retrieval, model transmission, and public-repository publication need separate authorization. Do not copy internal FinOps content into XPPGen as a retrieval cache or evidence shortcut.

Preflight records unavailable information as unknown/blocked, not as fabricated defaults. No quantitative search SLA is promised before representative measurements.

## Success metrics

Use the frontmatter metrics on reviewed qualification cases with known expected reuse/extension outcomes. Record correctness and unresolved-evidence rates separately from search latency.

## Out of scope

Code generation, metadata mutation, compiling/deploying, automatic infrastructure setup, and claims about the uninspected msdyneng repository.

## Dependencies and implementation gates

Requires approved source locations, version inventory, metadata/reference adapters, and an owned-model/target policy. FinOps retrieval interfaces and repository coordinates are not yet supplied.

See [implementation-design.md](implementation-design.md) for proposed provenance and capability contracts.
