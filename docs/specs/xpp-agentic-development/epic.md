---
type: epic
feature: xpp-agentic-development
journey_ref: docs/ux/xpp-agentic-development-journey.md
children:
  - id: discovery-grounding
    spec_ref: discovery-grounding-prd.md
    stage_refs: [frame-business-outcome, decide-reuse-or-extension]
  - id: authoring-build
    spec_ref: authoring-build-prd.md
    stage_refs: [decide-reuse-or-extension, engineer-and-compile-change]
  - id: test-generation
    spec_ref: test-generation-prd.md
    stage_refs: [engineer-and-compile-change, prepare-tests-and-delivery]
  - id: ude-verification
    spec_ref: ude-verification-prd.md
    stage_refs: [prepare-tests-and-delivery, verify-in-approved-ude]
  - id: evidence-handoff
    spec_ref: evidence-handoff-prd.md
    stage_refs: [prepare-tests-and-delivery, verify-in-approved-ude, preserve-evidence-and-handoff]
---

# X++ Agentic Development Epic

## Goal - Problem / Solution / Impact

For an intermediate X++ developer, typing code is relatively straightforward. The difficult work is discovering whether a business capability exists, selecting the right extension boundary, producing correct and performant changes, and deploying/testing them safely.

Build a developer-supervised Copilot Chat experience in VS2022 around those decisions. Start from the companion's VSIX, MCP server, and in-process Dynamics metadata bridge architecture, adding explicit capability, ownership, compilation, test, approval, and evidence contracts.

The intended impact is less redundant customization, less unsupported API use, faster evidence-backed implementation, and an honest separation between compiling artifacts and verified runtime behavior.

## Personas and journey

Primary persona: `xpp-developer`, defined in the [developer journey](../../ux/xpp-agentic-development-journey.md). The developer may hand generated browser tests to a tester or a Playwright-capable tool; this does not introduce a separately specified persona or an automatic external-execution guarantee.

Paths supported:

- Existing functionality satisfies the request: discovery and a useful no-code handoff.
- Customization is needed: approved local changes, successful compilation, generated tests/runbooks, runtime deferred.
- Runtime verification is requested: approved UDE deployment/synchronization and tests, followed by actual evidence.
- Work is interrupted or blocked: state and pending actions persist without claiming success.

## Child specifications

| Child | Responsibility | Prerequisites |
|---|---|---|
| [Discovery and source grounding](discovery-grounding-prd.md) | Business framing, read-only preflight, existing-functionality assessment, versioned retrieval | Working development setup; authorized reference sources |
| [Authoring and compilation](authoring-build-prd.md) | Approved model/object changes, ownership, performance reasoning, compiler diagnostics | Discovery decision and compatible metadata/build adapters |
| [Test artifact generation](test-generation-prd.md) | SysTest, executable Playwright TypeScript, runbooks and data/evidence contracts | Confirmed behavior and implementation plan; can start before implementation completes |
| [Approved UDE verification](ude-verification-prd.md) | Optional cloud deploy/sync and runtime test execution/manual handoff | Compiled artifacts, generated tests, target-specific approvals |
| [Evidence and handoff](evidence-handoff-prd.md) | Accurate statuses, sanitized source-controlled artifacts, retained-record manifests and resume | Cross-cutting from discovery onward; does not require runtime execution |

These are coherent specification slices, not five isolated applications. Shared contracts are proposed in [implementation-design.md](implementation-design.md).

## Functional requirements

1. Clarify the outcome before treating code generation as necessary.
2. Retrieve relevant existing metadata/source/tests with version and authorization provenance; never infer absence solely from an empty search result.
3. Approve an explicit plan covering objects, owned custom models/packages, dependencies, behavior, risks, and tests.
4. Write metadata through supported Dynamics APIs, verify source/metadata and placement, and compile with the installed X++ toolchain.
5. Generate SysTest and Playwright TypeScript tests plus a human-readable runbook with meaningful assertions.
6. Optionally deploy/synchronize and execute runtime tests under explicit target and effect approvals.
7. Preserve sanitized code/documents/scripts/screenshots/results together on a task branch and obtain separate approval for remote push/PR.

## Non-functional requirements

- Correctness and honest status reporting take precedence over appearing fully automated.
- The chat model cannot override backend ownership, target, publication, or side-effect checks.
- All cloud operations are attributable to a selected target and exact artifacts; unknown outcomes are reconciled before retry.
- Discovery and local repair are bounded and observable. Numeric latency, size, and retry budgets remain open until measured and approved.
- Retrieved private source is not copied into this public repository. Approved model access and repository publication are separate policy decisions.
- Source and installed application compatibility must be established before generating against engineering-only APIs.

## Workflow-family coverage

Final scope retains standard table/form extensions, CoC/events, new custom data/UI/menu/security features, batch processing, and entities/services.

Each release declares supported operations and concrete subtypes. Do not advertise support merely because an object type appears in a menu, generated schema, or prompt. Unsupported operations provide a supported manual step or an explicit blocker.

The first recommended vertical qualification case is a standard table/form field change with validation and tests. The exact base objects, functional requirement, supported version, and representative data must be selected before implementation; none is invented here.

## Delivery threshold and product qualification

For an individual customization, successful compilation plus generated tests/runbooks can satisfy delivery. Report runtime checks as deferred/not run where applicable. An existing-functionality answer needs evidence-backed guidance, not an unnecessary build.

To claim the product can perform a runtime workflow, separately demonstrate it on a supported installation with approved UDE access. Passing documentation checks does not establish product capability.

## Success metrics

- Every acceptance criterion has a valid journey-stage reference and remains `pending` until executed.
- Every qualification-case reuse/extension decision includes applicable evidence or an explicit uncertainty/blocker.
- Every declared compiled delivery has an attributable successful build and disclosed warnings.
- Every runtime success claim has actual operation/test evidence; no generated, skipped, blocked, or unknown check is counted as passed.
- No unapproved standard-model write, cloud effect, or source-control publication is permitted in qualification tests.
- Quantitative improvement in discovery/build/deployment time is measured against a recorded baseline; no target is asserted before that baseline exists.

## Recommended delivery sequence

1. Establish source/version, writable-model, target, publication, and capability contracts.
2. Qualify discovery, metadata-extension authoring, compilation, generated tests, and artifact handoff for the first vertical case.
3. Qualify approved UDE execution and add custom data/UI/security plus CoC/event cases.
4. Qualify batch and entity/service cases with their specific volume, retry, authorization, and external-effect requirements.

The sequence is a recommendation, not a user-approved schedule or effort estimate.

## Open implementation gates

- Exact FinOps repository/local source location, authorized retrieval scope, and release/build matching.
- Supported VS/Dynamics/compiler/application versions and Copilot model availability.
- Companion code-reuse rights and the API compatibility of required adapters.
- Owned model allowlist, target environment/company identities, and approved repository/data policy.
- Executable Playwright runner configuration and supported UI variants.
- Quantitative limits, evidence sanitation/retention, and concrete performance budgets.

## Out of scope

Production promotion, automatic environment provisioning, a separate Claude client, direct standard/ISV modification, raw metadata XML fallback, secret publication, automatic default-branch writes or merges, and product implementation during this specification phase.

## Recommended next step

Run `spec-vs-journey-review` across the epic and child PRDs before test-first implementation planning.
