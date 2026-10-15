---
type: user-journey-map
feature: xpp-agentic-development
jtbd: "When I receive a D365FO change request, I want to determine whether existing functionality can satisfy it and, only where necessary, build a correct and performant extension, so I can deliver the business outcome without redundant customization or unsafe UDE changes."
personas:
  - id: xpp-developer
    role: "X++ developer or technical consultant"
    context: "Daily normal development in Windows and Visual Studio 2022, with interruptions but time to review the agent's recommendations and approve consequential actions."
    skill_level: intermediate
stages:
  - id: frame-business-outcome
    name: "Frame the business outcome and working context"
    persona: xpp-developer
    user_action: "Describe the problem, affected business process, expected behavior, and relevant examples; review the read-only environment and model preflight."
    user_thought: "Do we understand the business need and know which application version, model, company, and environment we are talking about?"
    user_feeling: "🧐 Cautious"
    pain_points:
      - "A request can describe a proposed customization rather than the underlying business need."
      - "Application versions, dependencies, and active environment settings can change what is possible."
      - "A local build may be configured to deploy or synchronize online."
    success_signal: "The developer confirms the intended outcome and constraints; known environment/model identity and unresolved preflight items are visible before any write."
    acceptance_criteria_refs: []
  - id: decide-reuse-or-extension
    name: "Find existing functionality and justify any extension"
    persona: xpp-developer
    user_action: "Review evidence from existing application behavior, relevant source/metadata, references, extension points, and tests; choose reuse/configuration or approve a justified customization direction."
    user_thought: "Does this capability already exist, and if not, what is the smallest supported change?"
    user_feeling: "🔎 Investigative"
    pain_points:
      - "Finding the right business capability and implementation is harder than writing the code."
      - "An extension can duplicate existing functionality or attach at the wrong business boundary."
      - "An engineering-source example may use an API unavailable in the target application version."
    success_signal: "A traceable decision identifies an existing solution or explains the necessary customization, verified target-version extension points, and remaining uncertainty."
    acceptance_criteria_refs: []
  - id: engineer-and-compile-change
    name: "Approve, engineer, and compile a focused change"
    persona: xpp-developer
    user_action: "Approve the object/model/dependency plan and performance considerations, then review generated changes, metadata read-back, compiler diagnostics, and bounded repairs."
    user_thought: "Will this preserve existing behavior, handle realistic data volume, and stay within the change I approved?"
    user_feeling: "⚖️ Deliberate"
    pain_points:
      - "Normalized relations, company scope, transaction boundaries, and date-effective behavior make plausible code incorrect."
      - "Compilation does not establish acceptable query or batch performance."
      - "Missing metadata-extension support, partial writes, or incorrect model placement can prevent a valid change."
    success_signal: "Approved changes are in the intended owned custom models, metadata/source checks succeed, compilation has zero errors, warnings are disclosed, and performance assumptions and unmeasured risks are explicit."
    acceptance_criteria_refs: []
  - id: prepare-tests-and-delivery
    name: "Prepare meaningful tests and choose the verification depth"
    persona: xpp-developer
    user_action: "Review SysTest cases, executable Playwright TypeScript tests, a human-readable runbook, prerequisite data, expected assertions, evidence checkpoints, and any performance-verification plan; choose to defer or proceed with runtime verification."
    user_thought: "Do these tests detect incorrect business behavior and regressions, or do they only demonstrate that a form can be saved?"
    user_feeling: "🧪 Evaluative"
    pain_points:
      - "Existing UDE data may lack required prerequisites or contain unrelated records."
      - "Generated tests can encode the same incorrect assumptions as the implementation."
      - "Screenshots and compilation can be mistaken for runtime correctness or performance evidence."
    success_signal: "Tests/runbooks are generated with observable expected outcomes and mutation boundaries; delivery can be recorded as compiled with runtime verification deferred, without claiming tests passed."
    acceptance_criteria_refs: []
  - id: verify-in-approved-ude
    name: "Optionally deploy and verify in the approved UDE"
    persona: xpp-developer
    user_action: "Approve the exact deployment/synchronization and test-data effects; deploy through a verified adapter or supported manual VS action, run SysTest, and execute or hand off Playwright scenarios."
    user_thought: "Am I testing the right build in the right company, and what will this change in the shared environment?"
    user_feeling: "🛡️ Vigilant"
    pain_points:
      - "Deployment, database synchronization, and cloud execution are distinct from local compilation."
      - "A test may change existing records, post transactions, or trigger batch/external effects."
      - "Unsupported automation, failed synchronization, or an unknown timeout outcome can require manual intervention."
    success_signal: "For executed work, target/build identity, deployment/sync results, test assertions, relevant measurements, screenshots, and per-step outcomes are recorded; pending, manual, blocked, and failed work is not reported as passed."
    acceptance_criteria_refs: []
  - id: preserve-evidence-and-handoff
    name: "Preserve evidence and hand off the change honestly"
    persona: xpp-developer
    user_action: "Review the delivery status, retained-record manifest and cleanup instructions; allow sanitized artifacts to be committed to the task branch under the approved policy, and separately decide whether to push or open a PR."
    user_thought: "Can another developer understand the change, see what was actually verified, and reproduce or continue the work?"
    user_feeling: "📋 Accountable"
    pain_points:
      - "Interrupted development or external test execution can leave context and evidence disconnected."
      - "Screenshots, logs, and manifests may expose restricted business data or authentication material."
      - "A single 'done' label can hide deferred runtime checks, unknown performance, or retained test data."
    success_signal: "The task branch contains the code, tests, runbooks/documents, and permitted sanitized evidence; verification gaps and cleanup obligations are explicit, and remote publication remains approval-controlled."
    acceptance_criteria_refs: []
---

# X++ Agentic Development Journey

## Framing and evidence

This journey is based on `..\brain-dumps\xpp-agentic-development-brain-dump.md` and the follow-up discovery interview. Architecture and source-review findings are in `..\xpp-agentic-development-review.md`.

The developer identified the main difficulty as determining whether functionality already exists or needs an extension, implementing the change performantly, and deploying/testing it. Typing X++ is not the primary problem.

The selected primary context is an intermediate developer doing daily normal development with review time. The primary failure to prevent is a change that compiles but is redundant, incorrect, slow, or causes unintended UDE side effects.

Thoughts and feelings below are design hypotheses derived from that context, not claims of observed usability research. Stage and persona IDs are stable references for downstream specification and tests. Acceptance-criteria references remain empty until the specification exists.

## Frame the business outcome and working context

Begin with the desired business result rather than accepting "write an extension" as a settled solution. Clarify the current and desired behavior, affected roles/processes, representative records, and constraints.

Use the already working VS2022/UDE setup; do not provision or reconfigure infrastructure. The read-only preflight identifies relevant versions, selected owned models, dependencies, and environment identity. Inspect automatic online deployment/synchronization settings so local compilation cannot bypass cloud approval.

**Success:** an agreed problem statement and known context, with missing information exposed. A failed or incomplete preflight blocks the dependent action rather than inventing a default environment.

## Find existing functionality and justify any extension

This is the central judgment stage. Search for an existing business capability, supported configuration, or reusable implementation before generating new objects.

Ground the assessment in relevant metadata, source, references, and tests. FinOps source in msdyneng can supply examples and framework context when access is authorized; verify applicability against the target application build. Broad source similarity or the absence of a search hit is not proof that a capability exists or does not exist.

Present the alternatives, supporting object/source references, constraints, and confidence. If existing functionality meets the need, a successful outcome may be usage/configuration guidance with no code change. Do not force a customization to satisfy a code-generation workflow. If configuration changes are proposed, treat their writes and effects as separately approved work, not as harmless discovery.

If customization is necessary, explain why and choose the smallest supported extension boundary. Standard and unowned ISV objects remain reference-only.

**Success:** the developer can explain why reuse is sufficient or why an extension is necessary, including unresolved assumptions.

## Approve, engineer, and compile a focused change

The approved plan names objects, target custom models/packages, any new model or dependency changes, extension mechanisms, intended behavior, and tests. Ordinary local edits/builds can then proceed within that scope.

Performance reasoning belongs here, before runtime problems are baked in. Inspect actual relations, intended filters and data volumes, available indexes, set-based versus per-record processing, transactions, company boundaries, and batch/integration effects as applicable. Do not claim an index or set-based rewrite is always appropriate; ground the choice in semantics and evidence.

Use the companion's VSIX/MCP/in-process metadata bridge architecture as the starting design. Where its current capabilities are missing, identify the gap and use a verified adapter or supported manual step; do not emit raw metadata XML as a shortcut.

Read back metadata/source, verify placement, and compile with the target toolchain. Surface warnings and repair only within the approved plan. An uncertain or partial write needs state reconciliation before retry.

**Success:** a focused, metadata-checked change compiles, and the developer sees both the performance rationale and what remains unmeasured.

## Prepare meaningful tests and choose the verification depth

Generate SysTest and executable Playwright TypeScript tests, plus a human-readable runbook. Cover the intended business outcome, meaningful negative/boundary cases, and preserved behavior where applicable. Data-creation screenshots alone do not establish correctness.

Use existing UDE records where appropriate and specify missing prerequisites. Tests need environment/company parameters, identifiable test records, expected persisted outcomes, approval boundaries, and stop conditions.

Each browser data-creation/save checkpoint needs a screenshot and saved step result. Include a manifest of created identifiers and explicit cleanup instructions. SysTest uses supported isolation/rollback where appropriate; browser-created records are retained for inspection.

Where performance is material, propose a representative verification scenario and state the required data-volume and measurement assumptions. Numeric thresholds require an agreed requirement; do not invent an SLA or imply that static inspection is a benchmark.

**Success:** compilation plus generated tests/runbooks satisfies the selected delivery threshold. The developer can choose runtime verification now or receive an explicit "compiled; runtime unverified" handoff.

## Optionally deploy and verify in the approved UDE

This stage is optional per delivery, not a prerequisite for generating test artifacts. To execute new code in UDE, however, the relevant compiled binaries must first be deployed.

Require explicit approval for the identified environment, package/build, and synchronization choice. Separately approve data-writing tests; existing-record changes, posting, batch effects, and external effects require scenario-specific approval.

Use verified automation interfaces. When an operation has no verified adapter, provide the exact supported VS action, pause, and collect the actual outcome. A Playwright-capable tool or the developer may execute the generated browser tests/runbook; external execution is not treated as completed until its results are available.

Record actual assertions, cloud operation outcomes, relevant performance measurements, screenshots, and step results. Stop on an unexpected company, build mismatch, denied permission, unintended effect, or unknown operation state.

**Success:** executed checks have evidence and accurate status. If work is deferred, manual, blocked, failed, or not run, say so. Do not infer cloud rollback from local source reversal or assume a timed-out mutation did nothing.

## Preserve evidence and hand off the change honestly

Commit code, SysTest, Playwright scripts, documents/runbooks, screenshots, and permitted sanitized evidence together on the task branch under the approved repository/data policy. Credentials and reusable authentication state must never enter the branch.

Automatic approved commits are allowed. An artifact whose sanitation cannot be established blocks its commit. Push/PR creation requires explicit approval; do not write directly to the shared default branch or auto-merge.

Preserve the reuse/extension decision, source/version provenance, implementation and compiler results, generated-versus-executed test status, measured-versus-assessed performance, deployment/sync status, retained-record identifiers, cleanup instructions, and pending work.

Interruptions or handoffs should not require another developer to repeat the original discovery. Retain enough context to resume after checking whether the source/environment changed.

**Success:** another developer can reproduce or continue the work and distinguish delivered artifacts from verified runtime behavior.

## Paths through the journey

- **Existing functionality is sufficient:** frame -> evidence-backed reuse decision -> handoff. Code generation and compilation are not required when nothing changes.
- **Compiled delivery with runtime deferred:** frame -> discovery -> engineering/compile -> generated tests/runbooks -> handoff with explicit runtime gaps.
- **Runtime-verified delivery:** follow all six stages, including approved cloud actions and actual test evidence.
- **Interrupted or blocked:** retain the last verified state, pending action, and required approval/evidence; resume only after rechecking relevant state.

## Recommended next step

Write the PRD and implementation specification using these stable stage IDs. Map acceptance criteria to every applicable stage, especially reuse/extension judgment, performance reasoning, approval boundaries, and truthful verification status. Validate source-grounding and tool-adapter assumptions with targeted capability probes before promising automation.
