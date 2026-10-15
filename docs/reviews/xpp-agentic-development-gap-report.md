---
type: spec-journey-gap-report
feature: xpp-agentic-development
spec_ref: docs/specs/xpp-agentic-development/epic.md
journey_ref: docs/ux/xpp-agentic-development-journey.md
verdict: gaps-found
---

# Gap Report - X++ Agentic Development

## Review scope and verdict

Reviewed the epic, all five child PRDs, implementation design, and structured developer journey from commit `34f12cbf85c6f682286db60b79375207a6aa6665`.

**Verdict: gaps-found.** All six stages are structurally covered, all 42 acceptance criteria have valid anchors and exact journey backlinks, and the primary persona is adequately defined. There are no wholly uncovered stages or ungrounded ACs.

The gaps concern the strength of qualification, not a missing end-to-end workflow. The criteria frequently require evidence, declarations or explicit uncertainty, but do not yet define how to establish decision quality, meaningful test sensitivity, runnable test-artifact quality, measured performance acceptance, or safe automatic evidence publication.

Some gaps are already acknowledged as open implementation gates. They are not hidden omissions, and the review does not invent their answers. They still prevent claiming the corresponding capabilities are qualified.

This report does not rewrite specifications, raise the user's delivery threshold, or claim that product acceptance tests were executed.

## Uncovered journey stages

None. Coverage details:

| Journey stage | ACs | Assessment |
|---|---|---|
| `frame-business-outcome` | `discovery-confirm-outcome`, `discovery-read-only-preflight`, `discovery-online-build-settings` | Covered: outcome/context capture, read-only discovery and prevention of hidden cloud effects are observable. |
| `decide-reuse-or-extension` | `discovery-authorized-provenance`, `discovery-existing-capability`, `discovery-extension-boundary`, `discovery-bounded-retrieval`, `discovery-no-code-outcome`, `authoring-approved-plan` | Structurally covered; decision-quality qualification is weak until G1 is resolved. |
| `engineer-and-compile-change` | `authoring-backend-ownership`, `authoring-capability-matrix`, `authoring-exact-placement`, `authoring-safe-metadata-write`, `authoring-model-dependencies`, `authoring-performance-rationale`, `authoring-attributable-build`, `authoring-local-only-build`, `authoring-repair-reconciliation`, `tests-requirement-trace` | Covered for safe authoring/build and assessed performance. Behavioral test sensitivity and measured-performance claims need G2/G4. |
| `prepare-tests-and-delivery` | `tests-systest-artifacts`, `tests-playwright-artifacts`, `tests-data-prerequisites`, `tests-step-evidence-contract`, `tests-retention-cleanup`, `tests-performance-verification-plan`, `tests-deferred-runtime-delivery`, `ude-optional-execution`, `evidence-status-dimensions` | Covered for generated artifacts and explicit runtime deferral; artifact validation and test-quality gates need G2/G3. |
| `verify-in-approved-ude` | `ude-target-bound-approval`, `ude-deployment-before-tests`, `ude-effect-bound-test-approval`, `ude-manual-supported-handoff`, `ude-real-systest-results`, `ude-browser-results`, `ude-unknown-outcome-recovery`, `evidence-step-manifest` | Covered for approved execution, identity and honest outcomes; performance-specific qualification needs G4. |
| `preserve-evidence-and-handoff` | `evidence-retained-records`, `evidence-source-control-bundle`, `evidence-policy-sanitation`, `evidence-separate-push-approval`, `evidence-resumable-context`, `evidence-no-code-handoff` | Covered for state, retention and source-control boundaries; automatic sanitation qualification needs G5. |

Counts are 3, 6, 10, 9, 8 and 6 respectively: **42 ACs total**.

## Ungrounded acceptance criteria (scope-creep risks)

None.

Each AC's `stage_ref` points to a real journey stage and belongs to its child PRD's declared stage slice. The six `acceptance_criteria_refs` lists match exactly; IDs are unique. All product `test_status` values remain `pending`.

Cloud approval, no-code outcomes, source privacy and runtime deferral are grounded in the journey rather than incidental additions. A separate tester persona is not necessary for the currently specified developer-led handoff; add one only if independent tester onboarding/operation becomes a separately supported user goal.

## Missing success metrics

Every stage has observable output/safety metrics. The distinction is between an **artifact/status metric** and a **quality qualification metric**:

| Stage and success signal | Existing measurable check | Missing or weak qualification |
|---|---|---|
| `frame-business-outcome`: confirmed outcome, known context and visible unresolved preflight items | Required recorded fields; zero writes in preflight; classification of online build effects | No specific missing metric for this signal. Real adapters/configuration still need capability probes. |
| `decide-reuse-or-extension`: traceable existing solution or justified customization with verified extension points | Evidence/provenance, bounded search, explicit uncertainty and no-code output | G1: no defined reference-case set, adjudication rubric or target for correct reuse/extension decisions. |
| `engineer-and-compile-change`: approved placement, verified metadata/source, zero compile errors, warnings and performance assumptions exposed | Zero-error attributable build; ownership/placement checks; recorded performance rationale | G2: expected behavior can remain weakly justified. G4: assessment is not a performance pass; runtime qualification needs a workload/budget. |
| `prepare-tests-and-delivery`: generated tests/runbooks, assertions and mutation boundaries; runtime deferral disclosed | Artifact presence, expected outcomes, prerequisites, screenshots/results contracts and not-run statuses | G2/G3: no explicit sensitivity check for generated assertions or required validation gate for the promised runnable artifacts. |
| `verify-in-approved-ude`: attributable deployment/test results and relevant measurements; no false pass | Approved identities/effects; actual per-test/step results; incomplete/failed outcomes rejected | G4: performance measurements and acceptance are discussed in prose but lack an explicit runtime performance AC and agreed scenario budget. |
| `preserve-evidence-and-handoff`: reproducible task branch, sanitized evidence, gaps/cleanup visible and publication controlled | Bundle/manifest/status checks, policy gate and approval boundaries | G5: "satisfied policy" cannot be qualified without a concrete policy and text/image acceptance and rejection cases. |

Numeric discovery latency and repair limits are openly deferred. This review does not manufacture them. A configured limit and its negative tests must exist before the corresponding bounded-operation claim can be qualified.

### G1 - Reuse/extension decision quality lacks a qualification oracle

**Relevant criteria:** `discovery-existing-capability`, `discovery-no-code-outcome`, `discovery-extension-boundary`.

**Evidence:** [discovery-grounding-prd.md](../specs/xpp-agentic-development/discovery-grounding-prd.md), AC block starting at line 27 and Success metrics at line 87.

The PRD already calls for reviewed cases with known expected outcomes and separate correctness/uncertainty rates. It does not define the case inventory, expected outcomes, adjudication owner/rubric, false-reuse versus unnecessary-extension handling, or acceptance threshold.

An answer containing references and an uncertainty field can meet the current output checks while still recommending the wrong business capability or a redundant extension. This is the developer's primary discovery pain.

**Recommended resolution:** agree a small release/build-matched reference set containing existing capability, configuration-only, extension-needed, and insufficient-evidence cases. Define expected evidence and how wrong conclusions and appropriate abstention are scored. Decide the release threshold rather than inventing one.

**Boundary:** source discovery and capability probes can begin before this is resolved; "qualified reuse/extension judgment" cannot.

### G2 - Test expectations are traced but their independence/sensitivity is not qualified

**Relevant criteria:** `tests-requirement-trace`, `tests-systest-artifacts`, `tests-playwright-artifacts`.

**Evidence:** [test-generation-prd.md](../specs/xpp-agentic-development/test-generation-prd.md), AC blocks starting at lines 7, 12 and 17.

Tracing a test to an approved outcome and naming an assertion is necessary. It is not sufficient to establish that the assertion detects incorrect behavior rather than reproducing the generated implementation's mistaken assumptions.

The PRD requires meaningful positive/negative/regression tests in prose, but lacks a qualification check against independently established expected results or known-bad behavior. A tautological assertion could satisfy the current artifact/trace metrics.

**Recommended resolution:** define independently reviewed expected outcomes for representative scenarios and demonstrate that qualified test patterns distinguish correct from known-incorrect behavior. Use safe fixtures or designated qualification cases; do not inject faults or mutate shared business data without approval.

**Boundary:** this is a product test-quality qualification gate, not a requirement to execute runtime tests for every individual delivery.

### G3 - Generated versus validated executable artifacts need a precise contract

**Relevant criteria:** `tests-systest-artifacts`, `tests-playwright-artifacts`, `tests-deferred-runtime-delivery`.

**Evidence:** [test-generation-prd.md](../specs/xpp-agentic-development/test-generation-prd.md), AC blocks at lines 12-21 and artifact-check discussion at line 84.

The spec intentionally permits test X++ to be explicitly uncompiled. It also promises executable TypeScript with verified prerequisites. Typechecking and compiling are correctly distinguished from runtime execution, but are not required by a specific artifact acceptance metric.

The implementation therefore lacks a precise boundary between "generated" and "validated runnable for the declared runner/toolchain." Non-placeholder code and verified selectors do not alone rule out syntax/type errors.

**Recommended resolution:** define artifact-quality states and checks: generated; syntax/typechecked against the declared Playwright runner; test X++ compiled against the target references; runtime not run/passed/failed. Decide which artifact checks are mandatory before using "executable/valid" labels and which can be deferred with explicit status.

**Boundary:** retain the selected compiled-code-plus-generated-tests delivery threshold. Do not silently make UDE execution mandatory.

### G4 - Performance assessment exists; measured performance acceptance is not specified

**Relevant criteria:** `authoring-performance-rationale`, `tests-performance-verification-plan`, runtime criteria in `ude-verification`.

**Evidence:** [authoring-build-prd.md](../specs/xpp-agentic-development/authoring-build-prd.md), AC at line 37; [test-generation-prd.md](../specs/xpp-agentic-development/test-generation-prd.md), AC at line 37; [ude-verification-prd.md](../specs/xpp-agentic-development/ude-verification-prd.md), non-functional requirement at line 84.

The current criteria correctly prevent treating compilation as a benchmark and allow missing budgets to remain explicit. That satisfies honest assessed/deferred status, not the broader goal of proving the code is performant.

No runtime AC defines the workload, baseline, measurement procedure, relevant acceptance budget, or inconclusive outcome for a material performance scenario.

**Recommended resolution:** select representative workload/data-volume and measurement conditions for each qualified data/batch scenario; agree a budget or baseline-relative criterion; define a performance-specific result independent of functional test success.

**Boundary:** functional runtime verification may succeed while performance remains unverified/inconclusive. A compiling, runtime-deferred delivery remains permitted. Do not impose arbitrary timing targets or performance tests on unrelated trivial changes.

### G5 - Automatic evidence commits depend on an undefined publication policy

**Relevant criteria:** `evidence-policy-sanitation`, `evidence-source-control-bundle`.

**Evidence:** [evidence-handoff-prd.md](../specs/xpp-agentic-development/evidence-handoff-prd.md), AC at line 27 and deferred image-redaction/storage policy at line 84; [implementation-design.md](../specs/xpp-agentic-development/implementation-design.md), Durable state and evidence.

The policy gate and fail-closed behavior are clear. The missing piece is the concrete policy and proof that automated text/image handling meets it for the intended publication target.

For a public repository, checking text for a few token patterns does not establish that screenshots, logs or record manifests are permitted. Detection of secrets is not a complete business-data or private-source publication policy.

**Recommended resolution:** approve permitted artifact classes and data fields, required redaction/exclusion, and deny/unknown handling. Qualify text and image acceptance/rejection cases. Keep credentials/authentication state prohibited and keep private FinOps content out of public artifacts.

**Boundary:** generic authored documentation with no private source/customer data is not equivalent to future business-data evidence. Do not claim automated screenshot publication is qualified just because documentation or text-secret checks passed.

## Underspecified personas

None for the referenced persona.

| Persona | Role | Skill level | Frequency/context | Coverage |
|---|---|---|---|---|
| `xpp-developer` | X++ developer or technical consultant | Intermediate | Daily normal Windows/VS2022 development, with interruptions and time to review | Defined in journey frontmatter; discovery PRD repeats the context and other children explicitly reference the journey. All stages and PRDs use this ID. |

Expert hotfix work, beginner onboarding, and an independent tester experience are not silently added. They need separate requirements only if promoted into scope.

## Unresolved pain points

Every pain point has at least one related AC. Six have partial follow-through because qualification strength is unresolved; twelve have clear specification-level follow-through. "Covered" means the requirement is present, not that its product behavior has passed a test.

| Stage | Journey pain | Related ACs | Follow-through |
|---|---|---|---|
| `frame-business-outcome` | A proposed customization can be mistaken for the business need | `discovery-confirm-outcome`, `discovery-existing-capability`, `discovery-no-code-outcome` | Covered: clarify and allow the evidence-backed no-code path. |
| `frame-business-outcome` | Versions, dependencies and environment settings change what is possible | `discovery-read-only-preflight`, `discovery-authorized-provenance`, `discovery-extension-boundary` | Covered: version/context and applicability checks or explicit blockers. |
| `frame-business-outcome` | A local build may deploy/synchronize online | `discovery-online-build-settings`, `authoring-local-only-build` | Covered: classify effects and block unsafe/unknown local builds. |
| `decide-reuse-or-extension` | Finding the right capability is harder than writing code | `discovery-existing-capability`, `discovery-bounded-retrieval`, `discovery-no-code-outcome` | Partial: retrieval/output criteria exist; correctness qualification needs G1. |
| `decide-reuse-or-extension` | An extension can duplicate functionality or target the wrong boundary | `discovery-existing-capability`, `discovery-extension-boundary`, `authoring-approved-plan` | Partial: verified boundaries/approval help; expected reuse-versus-extension judgments need G1. |
| `decide-reuse-or-extension` | Engineering examples may use unavailable APIs | `discovery-authorized-provenance`, `discovery-extension-boundary` | Covered: version applicability and explicit blocking of unverified APIs. |
| `engineer-and-compile-change` | Relations, company, transactions and dates can make plausible code wrong | `authoring-performance-rationale`, `tests-requirement-trace`, `authoring-safe-metadata-write` | Partial: reasoning and trace exist; behavioral sensitivity needs G2. |
| `engineer-and-compile-change` | Compilation does not establish query/batch performance | `authoring-performance-rationale`, `tests-performance-verification-plan`, `evidence-status-dimensions` | Partial: disclosure is covered; a measured performance pass needs G4. |
| `engineer-and-compile-change` | Missing extension support, partial writes or wrong placement prevent valid changes | `authoring-capability-matrix`, `authoring-exact-placement`, `authoring-safe-metadata-write`, `authoring-repair-reconciliation` | Covered: operation-specific capability, placement, validation and reconciliation. |
| `prepare-tests-and-delivery` | Existing data can lack prerequisites or contain unrelated records | `tests-data-prerequisites`, `ude-effect-bound-test-approval` | Covered: selection/prerequisites, record boundaries and effect approvals. |
| `prepare-tests-and-delivery` | Tests can share the implementation's wrong assumptions | `tests-requirement-trace`, `tests-systest-artifacts`, `tests-playwright-artifacts` | Partial: trace exists; independent expectation/sensitivity checks need G2. |
| `prepare-tests-and-delivery` | Screenshots and compilation can be mistaken for runtime proof | `tests-step-evidence-contract`, `tests-deferred-runtime-delivery`, `evidence-status-dimensions` | Covered: persisted assertions and generated-versus-executed status. G3 separately concerns artifact validity. |
| `verify-in-approved-ude` | Cloud execution is distinct from local compilation | `ude-target-bound-approval`, `ude-deployment-before-tests` | Covered: explicit deployment/sync and artifact identity before execution. |
| `verify-in-approved-ude` | Tests can modify records, post or cause external/batch effects | `ude-effect-bound-test-approval`, `tests-data-prerequisites`, `evidence-retained-records` | Covered: separate scope approval, record boundaries and retained effects. |
| `verify-in-approved-ude` | Missing automation, failed sync or unknown outcomes need intervention | `ude-manual-supported-handoff`, `ude-unknown-outcome-recovery`, `ude-deployment-before-tests` | Covered: supported manual steps, pending status, evidence and reconciliation. |
| `preserve-evidence-and-handoff` | Interruptions and external execution disconnect context/evidence | `evidence-resumable-context`, `evidence-step-manifest`, `ude-browser-results` | Covered: verified state and attributable external-result checks. |
| `preserve-evidence-and-handoff` | Screenshots/logs/manifests may expose restricted data or authentication | `evidence-policy-sanitation`, `discovery-authorized-provenance` | Partial: gating/prohibitions are clear; actual policy and sanitation qualification need G5. |
| `preserve-evidence-and-handoff` | A single done label hides deferred checks, performance or retained data | `evidence-status-dimensions`, `evidence-retained-records`, `tests-deferred-runtime-delivery`, `evidence-no-code-handoff` | Covered: separate statuses, cleanup obligations and honest no-code/deferred paths. |

## Recommended next actions

1. Approve the intended gap resolutions before editing the PRDs. Preserve optional runtime verification and the selected delivery threshold.
2. Identify the exact authorized FinOps source/release snapshot and target application/tool versions. Build the small reviewed decision/test case set for G1/G2 without publishing private source.
3. Agree the artifact labels and compile/typecheck requirements for G3; keep generation separate from runtime execution.
4. Select representative performance scenarios and agreed measurement/budget rules for G4. Permit an explicit unverified/inconclusive performance result where evidence is unavailable.
5. Define and qualify the publication policy for G5 before automatic commits of real screenshots/logs/customer-data manifests.
6. Run targeted, approved capability probes for the chosen first vertical case. Distinguish source/tool availability from actual metadata/compiler/UDE capability; do not mutate UDE or install tools as a read-only probe.
7. Update the affected specs only after decisions, then rerun `spec-vs-journey-review`. A complete review can hand off to `tdd-from-journey`.

Structural coverage is strong enough that a full requirements restart is not warranted. This is not a `major-gaps` verdict; `grill-spec` is optional if the targeted decisions need a deeper interview.

## Validation performed

- Parsed the journey, epic and all child PRD YAML.
- Verified six real stages, one referenced persona, 42 globally unique anchored ACs and exact journey backlinks.
- Confirmed all 42 product criteria remain `pending`.
- Read and semantically mapped all 18 pain points and six success signals.

No private FinOps code, native X++ metadata tool, compiler, Playwright browser, UDE deployment, or product acceptance test was exercised. Structural documentation validation is not product qualification.
