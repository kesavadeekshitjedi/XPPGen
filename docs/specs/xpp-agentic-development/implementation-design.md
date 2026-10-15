# XPPGen Implementation Design

Status: proposed implementation contracts derived from the agreed requirements. No product code, private FinOps source, installed-version API probes, compiler runs or UDE tests have been performed as part of this specification phase.

This document complements the [epic](epic.md) and child PRDs. It is not an assertion that the companion already implements these interfaces.

## 1. Starting architecture

Retain the reviewed architecture:

```text
Copilot Chat Agent mode in VS2022
    -> native MCP calls
    -> local companion MCP server
    -> in-process Dynamics metadata bridge in VS
    -> installed Dynamics metadata/project services
```

Add bounded source-grounding, approval/state tracking and adapters for compiler, UDE, tests, evidence and source control. Do not replace the metadata bridge with raw XML generation or require a direct private language-model API.

The backend remains independent of the selected model. Claude, where available, is a model selection within Copilot Chat, not a separate client.

### Proposed responsibilities

| Boundary | Responsibility | Must not do |
|---|---|---|
| Chat host | Clarify, reason, present plans, invoke discovered tools | Override backend ownership/approval policy or invent tool results |
| MCP surface | Versioned capabilities, typed requests/results, operation identity | Drop validation fields or convert unknown outcomes to success |
| VS metadata bridge | UI-thread-safe installed API access and exact object/project operations | Treat arbitrary model names as owned or silently retarget |
| Source-grounding adapter | Authorized, versioned, focused retrieval and provenance | Export private source into public evidence or assert branch compatibility |
| Build adapter | Local-only X++ compile and attributable diagnostics/artifacts | Trigger online deploy/sync under local-plan approval |
| UDE/test adapters | Approved effects, supported operation/status/logs and selected tests | Use guessed internal APIs or blindly replay cloud mutations |
| Artifact/Git adapter | Policy-compliant manifests, bundles, task commits and authorized publication | Commit authentication state or infer push/merge permission |

These are logical boundaries; file/class/process choices should follow proven API constraints rather than introduce unnecessary services.

## 2. Capability discovery

Return capabilities per operation, type/concrete subtype, installed version and execution mode:

```text
operation
objectType / concreteSubtype, where applicable
mode: automated | supported-manual | unsupported | unverified
adapterVersion
compatibleToolApplicationVersions
evidenceLocator
limitations
```

A reflective type schema is not a capability proof. Validate create and update independently, including metadata extensions, non-string EDTs, queries, nested form source, security membership, services and model/dependency operations.

### Companion gaps to address

- Extension types are advertised but absent from current create dispatch/schema.
- Nine update types are implemented despite broader README coverage.
- `AxEdt` creation is hard-coded to string; queries use `AxQuerySimple`.
- Typed collection writes replace supplied children and may skip unsupported properties.
- Current project validation can accept same-model objects without exact membership.
- Nested metadata validation is serialized by the bridge but not preserved in the generic MCP response.
- Explicit model resolution can fall back, and ownership hints do not constitute backend policy.
- Creation can persist a blank object before metadata application fails.
- Build/deploy/sync/SysTest/model-create/dependency-change adapters are not advertised.

The direct menu language-model call is a stub. The design uses the actual Copilot MCP interaction and verifies instruction/context behavior there.

## 3. Source grounding

### Design-time review

Once exact authorized sources are available, inspect representative framework implementations and tests to establish verified recipes, extension guidance and evaluation cases.

### Per-request retrieval

1. Capture the desired business outcome and target version.
2. Locate existing capability and relevant object definitions using focused discovery.
3. Expand through applicable relations/references and supporting tests.
4. Check example API and extension-point applicability against the target build.
5. Return only necessary approved context, with provenance and uncertainty.
6. Present reuse/configuration/extension alternatives before object creation.

Proposed provenance:

```text
sourceId and authorized locator
repositoryRevision or metadataSnapshotId
applicationBuild / compatibilityAssessment
object / symbol / relatedTest
retrievalPurpose and boundedScope
authorizationForModelContext
authorizationForPublication
truncation / staleReference / missingEvidence
```

Engineering-source access does not imply that any given source may be sent to a chosen model. Model-context authorization does not imply public publication permission.

The target's supported metadata/APIs govern generation. A newer msdyneng branch can inform design but cannot make an unreleased API available in UDE.

No private source retrieval is configured in this specification.

## 4. Plan and approval contracts

Proposed plan:

```text
planId / revision
businessOutcome and reuseExtensionDecision
sourceProvenance
objects: type, name, targetOwnedModel, package, operation, expectedState
modelCreation / dependencyChanges
behavior and preservationRequirements
performanceAssumptions / verificationNeeds
tests and dataPrerequisites
localMutationScope / buildScope
cloudEffects / publicationIntent
risks and unresolvedRequirements
```

Approval is bound to the relevant plan/build/target/scope, not a free-form global "yes." Separate domains:

| Domain | Approval covers |
|---|---|
| Local plan | Declared owned custom-object/model/dependency edits and local compilation |
| Deploy/sync | Identified UDE, artifact/package set and explicit sync choice |
| Data-writing test | Identified scenario/company/record scope and declared effects |
| Elevated scenario effects | Existing-record changes, posting, batch or external effects |
| Publication | Intended remote/branch/push or PR, under the repository/data policy |

Invalidated or materially changed context requires renewed approval. Exact expiry/policy mechanism is an implementation decision; no fabricated duration is required by the PRDs.

Verify effective backend ownership and permission, not just an approval string. Treat Microsoft and unowned ISV models as reference-only.

## 5. Metadata and compilation

Use JSON and plain X++ source through installed Dynamics APIs. Read before changing, preserve unrelated metadata, and verify the exact expected state afterward.

Proposed result:

```text
operationId
outcome: succeeded | failed | blocked | pending | unknown
targetIdentity
mutationState: none | complete | partial | unknown
validation: intendedMetadata, intendedSource, modelPlacement, projectMembership
diagnostics and unsupportedInputs
logs / evidenceReferences
```

Do not use a same-model heuristic as proof of project membership. Do not silently fall back to the active model when an explicit target is invalid. Unsupported graph inputs require explicit errors.

Before build, inspect effective online deployment/sync behavior. Use a verified local-only path or block; merely promising not to deploy does not change project settings.

Proposed build result:

```text
buildId / sourceScopeIdentity
compilerToolVersion / applicationReferences
outcome and exit/completionState
errors / warnings: code, message, object/method/location where available
logs
producedArtifactIdentity
cloudEffects: none | explicit-approved | unknown
```

Define the exact artifact identity mechanism after probing the real toolchain. Include the test-code build scope separately when necessary. Failed or missing results cannot become compiled.

Repairs are bounded and remain within the approved plan. Unknown/partial writes are reconciled before retry. Numeric budgets are unresolved implementation gates.

## 6. Performance reasoning and measurement

Assess data access using actual relations, company/date-effective behavior, selection criteria, volume assumptions, keys/indexes, transaction/concurrency behavior, and asynchronous or external effects.

Record:

- Why the selected extension boundary preserves behavior.
- Why the processing/query strategy fits the expected workload.
- Relevant constraints and tradeoffs, not a blanket rule that set-based processing or extra indexes always improve behavior.
- A measurable runtime scenario and agreed budget where performance is material.
- Whether evidence is assessed, generated, measured or inconclusive.

A successful compiler run is never a benchmark. If the budget or representative data is missing, report it rather than inventing a pass threshold.

## 7. Tests and browser handoff

Generate both SysTest and Playwright TypeScript with matched runbooks for supported scenarios. Expected results trace to agreed behavior and relevant existing tests, not just to the new implementation.

Declare runner/UI/version prerequisites and verify selectors/navigation. Complete executable coverage is conditional on those verified prerequisites; otherwise expose a blocker and do not label placeholders runnable.

Authentication is interactive or externally supplied through an approved mechanism. Reusable state stays outside source control.

Proposed step result:

```text
runId / scenarioId / stepId
buildIdentity / environment / company / roleContext
timestamp
action / expectedOutcome / observedOutcome
status: passed | failed | blocked | skipped | not-run | unknown
screenshotReference
persistedRecordIdentifiers
messages and approvedEffects
```

Every creation/save checkpoint requires persisted-state verification and a screenshot/result. Browser-created records remain tagged/identified for inspection, with cleanup instructions. Do not assume every table permits a custom tag field; use approved identifiers/manifest conventions where tagging in-record is not supported.

SysTest uses supported isolation/rollback. Posting, asynchronous processes, browser changes and external calls need their own effect handling.

An external executor returns results bound to the requested scenario/target/build. Missing or conflicting provenance prevents a verified status.

## 8. UDE execution

New binaries must be deployed before tests execute them in cloud. Keep deployment/synchronization and test approvals distinct.

Use demonstrated installed-version APIs; otherwise provide supported VS actions and require attributable completion/logs. Do not invent a deployment API from the existence of a menu command.

Proposed cloud-operation result:

```text
operationId / approvedScope
environment and artifactIdentity
kind: deploy | sync | systest | browser-scenario
mode: automated | manual | external
status and observedEffects
started/completed timestamps, where available
logs and testResults
reconciliationRequired
```

A timeout is not proof of cancellation. Reconcile unknown operations before any retry; rollback/redeployment is not assumed to reverse schema/data/posting effects.

Record intended user-role context for browser authorization checks. A successful developer-admin run does not establish end-user access.

## 9. Durable state and evidence

Keep separate state dimensions for:

```text
decision and plan revision
metadata/source verification
compile/artifact validation
deployment/sync
SysTest/browser execution
performance assessment/measurement
evidence sanitation/commit
remote publication
```

Persist last verified state, pending actions, relevant source/build/target identities and approval requirements. Recheck changed context when resuming; do not replay unknown mutations.

Store permitted code, tests, documents, screenshots and results together under the approved repository policy. A proposed run bundle contains a manifest, sanitized logs/results, screenshots and retained-record/cleanup details. Exact directory layout is not required by the PRDs.

For a public repository, only explicitly permitted sanitized artifacts may be published. Never copy raw internal retrieval content, customer records or browser authentication into evidence. Unknown/failed policy checks block affected commits. Automated screenshot sanitation technology must be qualified before claiming it safely removes restricted content.

Local task commits may be automatic under approved policy. Remote push/PR requires separate approval; no auto-merge/default-branch writes.

## 10. Capability probes before implementation promises

| Probe | Evidence required |
|---|---|
| Copilot host/MCP | Supported VS servicing version, model-policy availability, tool discovery and native tool calls |
| Source grounding | Authorized source locator/revision, target-version applicability, bounded retrieval and publication exclusions |
| Metadata extensions | Representative create/read/update/read-back on owned models through real installed APIs |
| Model/dependencies | Verified API or supported manual workflow with checked placement/references |
| Compiler | Local-only build, structured diagnostics, source/artifact identity and no unintended online effects |
| Deploy/sync | Approved target/artifact operation, completion and logs; manual path if no verified API |
| SysTest | Discovery/execution and attributable cloud outcomes for deployed test binaries |
| Playwright | Supported runner, authenticated live UI navigation, assertions and creation/save evidence |
| Evidence/Git | Approved policy, sanitation behavior, denied-secret/restricted-artifact cases, task branch and publication approval |

These probes are future implementation work, not completed tests. Private source coordinates and supported versions remain open.

## 11. First vertical case

Recommended: determine whether a requested field/validation behavior exists, then, if necessary, add a standard table/form metadata extension and behavior/test classes in an approved custom model.

Before code:

1. Select an actual requirement, target objects/version and existing-data context.
2. Establish authorized source/reference retrieval and expected reuse/extension outcome.
3. Prove the required metadata-extension/compiler operations.
4. Define the approved model/target/data/publication scopes.
5. Generate test/runbook contracts and qualify the intended UI runner.

Delivery can stop at compiled code and generated artifacts with runtime unverified. Qualification of runtime capability continues through explicitly approved deployment/sync, test execution and sanitized evidence.

## 12. Next specification step

Run `spec-vs-journey-review` against the epic and five child PRDs. Resolve traceability gaps before `tdd-from-journey` or product implementation. Do not mistake documentation validation for a completed code/runtime qualification.
