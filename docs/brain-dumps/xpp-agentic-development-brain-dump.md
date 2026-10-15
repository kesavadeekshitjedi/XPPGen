---
type: brain-dump
feature: xpp-agentic-development
scope: epic
personas:
  - X++ developer or technical consultant supervising Copilot Chat in Visual Studio 2022
  - Developer or tester executing the generated browser runbook and Playwright tests
goals:
  - Determine whether existing functionality satisfies the business need before creating an extension
  - Engineer correct and performant changes rather than merely generate compiling code
  - Converse about a D365FO problem and turn it into a grounded object and implementation plan
  - Create or extend X++ objects in approved custom models and check compilation
  - Generate X++ SysTest and executable Playwright TypeScript tests with human-readable runbooks
  - Use existing UDE data and create missing test prerequisites with screenshots and per-step outcomes
  - Help deploy and synchronize approved changes to the selected UDE
  - Preserve code, documents, tests, screenshots, and sanitized evidence together in source control
constraints:
  - Use Copilot Chat inside VS2022; Claude models are optional when subscription and policy permit
  - Assume an already working VS2022/D365FO/UDE setup and validate it with read-only preflight
  - Use verified Dynamics metadata APIs and JSON tool payloads rather than raw metadata XML editing
  - Restrict writes to explicitly approved owned custom models; standard and unowned ISV models are reference-only
  - Require explicit approval for UDE deployment/synchronization and data-writing tests
  - Require separate scenario-specific approval for existing-record changes, posting, external effects, and batch effects
  - Never commit credentials, tokens, cookies, or reusable browser authentication state
  - Automatically commit sanitized evidence only under an approved repository/data policy
  - Use task branches; require approval for push/PR and never auto-merge
  - Compilation plus generated tests/runbooks is sufficient for delivery, but deferred runtime testing must be disclosed
  - Start from the GitHub companion architecture; verify source-reuse rights and installed-version compatibility before implementation
out_of_scope:
  - Autonomous production deployment or promotion beyond the approved UDE
  - Direct modification of Microsoft or unowned ISV metadata
  - Automatic tool installation, environment provisioning, or identity setup
  - A separate Claude IDE extension or Claude Code integration
  - Raw metadata XML generation as a fallback for missing tool capabilities
  - Committing secrets or browser authentication state
  - Direct commits to a shared default branch or automatic PR merge
  - Product implementation during this requirements/design task
open_questions:
  - Which application, compiler, Dynamics tools, and VS servicing versions must be supported?
  - Which local ApplicationSuite and dependency/custom metadata are available and approved for assistant retrieval?
  - Which owned models, environment/company identities, repository, and data policy are approved?
  - Which parts of the companion implementation may be reused, and which need changes for the supported tool versions?
  - Which build, UDE deployment/synchronization, and SysTest operations have verified automation adapters on the supported versions?
  - What are the precise sanitation, evidence retention, repository size, record-tagging, and cleanup policies?
  - What Playwright runner/configuration and UI variants must executable tests support?
  - What quantitative latency targets and retry/repair limits are required?
---

# X++ Agentic Development Brain Dump

## Problem and intended experience

A developer should be able to explain a D365FO problem in Copilot Chat inside Visual Studio 2022, resolve ambiguities with the agent, approve a grounded implementation plan, and receive X++ changes that compile.

The experience also generates SysTest and browser acceptance tests, helps prepare realistic UDE test data, records screenshots and step outcomes, and assists with approved UDE deployment and synchronization. Runtime execution is available as a workflow goal, but it may be deferred for an individual delivery.

The companion repository supplies part of the metadata-tool foundation. It does not currently provide the complete lifecycle. See `..\xpp-agentic-development-review.md` for the pinned source review, object-creation catalog, operation gaps, examples, and authoritative UDE references.

## Decisions made during grilling

### Persona, scope, and host

- The primary user is an X++ developer or technical consultant, not an unsupervised nontechnical business user.
- Journey discovery refined this to an intermediate developer using the experience daily for normal Windows/VS2022 development, with review time.
- The principal friction is finding existing functionality versus necessary extensions, implementing performantly, and deploying/testing; writing the code itself is relatively straightforward.
- The primary failure is a redundant, incorrect, or slow change despite successful compilation, including unintended UDE side effects.
- The final scope includes standard table/form customization, CoC/events, new custom table/form/menu/security features, batch processing, entities/services, and real customer requests.
- Delivery is phased; none of those families is removed from the final scope.
- Use Copilot Chat in VS2022. Claude is a model choice within that host if supported by subscription and organizational policy, not a separate client requirement.
- Assume a working Dynamics development installation and UDE connection. Preflight validates it; environment/tool provisioning is separate.
- Start from the GitHub companion's VSIX, MCP server, and in-process Dynamics metadata bridge design. Requirements/design still precede product implementation; actual code reuse remains subject to rights and compatibility.

### Planning, ownership, and authority

- The agent clarifies the problem, discovers relevant metadata and APIs, and proposes objects, behavior, tests, placement, dependencies, and risks before writing.
- After plan approval, it may make approved local edits and run local builds automatically.
- The approved plan may include creating custom models/packages and changing dependencies. Those operations are not restricted to manual setup, but their implementation must use verified supported interfaces.
- Standard Microsoft and unowned ISV models remain reference-only. A model-name heuristic is not sufficient proof of ownership.
- UDE deployment and database synchronization need explicit approval naming the target.
- A local build must not bypass that boundary through automatic online deployment/synchronization settings.
- Tests that write business data need approval. Changes to existing records, posting, batch effects, and external effects require separate scenario-specific approval.
- If a supported automation interface is unavailable, provide the exact supported VS action, pause for the user, and collect evidence. Do not disguise manual or blocked work as automated success.

### Test layers and data

- Generate both X++ SysTest and browser acceptance tests. Neither substitutes for compilation or the other test layer.
- Use existing UDE data to ground scenarios and identify prerequisites.
- Missing prerequisites can be created by the user or their chosen browser automation tool using generated instructions/tests.
- Produce executable Playwright TypeScript tests and a human-readable runbook. Do not promise universal compatibility across arbitrary Playwright tools and UI versions.
- Each data-creation/save step needs a screenshot and saved outcome. Include expected assertions and verify persistence, not just appearance.
- Use test records by default. Identify the legal entity and records, and stop if the actual target differs from the approved target.
- Retain tagged browser-created test records for inspection, with an identifiers manifest and explicit cleanup runbook.
- SysTest should use framework isolation/rollback where appropriate. Do not assume rollback can undo browser actions, posted transactions, external calls, or asynchronous jobs.

### Source control and evidence

- Source-control code, SysTest, Playwright scripts, documents/runbooks, screenshots, and sanitized execution evidence together.
- Automatic evidence commits are allowed under an approved repository/data policy. A mandatory human review of every evidence bundle was not selected.
- Always exclude secrets, credentials, cookies, tokens, and reusable browser authentication state.
- If sanitation cannot safely establish that an artifact may be committed, stop the commit and report the artifact as blocked.
- Use a task-specific branch with automatic approved commits. Push and PR creation need explicit approval; no automatic merge.
- UDE execution approval is separate from permission to publish source-control changes.

### Delivery versus runtime verification

- The selected delivery threshold is successful compilation plus generated tests/runbooks. Runtime testing may be deferred.
- Report that outcome as compiled with test artifacts prepared and runtime unverified.
- Generated tests are not passed tests. A local build is not a successful deployment. Screenshots are supporting evidence, not standalone correctness assertions.
- When runtime execution is requested, deployment/sync, SysTest, and browser results remain individually visible and evidence-backed.
- A manual or external step stays pending until actual results are supplied. Failed, blocked, skipped, and unexecuted checks must not be counted as passed.

## Object-creation implications

The reviewed companion advertises 16 MCP tools and dispatches creation for 19 base metadata types. Only nine types have update implementations in the reviewed dispatcher. Metadata extensions advertised in the UI are not implemented in the creation dispatcher.

CoC/event/SysOperation/SysTest patterns can be represented using class objects, but a class cannot replace a required table/form metadata extension. Runtime schema discovery does not prove create/update support.

The design needs per-operation capabilities, verified concrete metadata subtypes, safe collection updates, ownership enforcement, exact placement/project checks, compiler diagnostics, cloud-operation evidence, and recoverable partial-state handling.

ApplicationSuite and FinOps engineering source in msdyneng would help ground business APIs, relations, extension points, framework patterns, and tests. Combine design-time review with per-request retrieval of relevant code and tests. Prefer release/build-matched snapshots and the matching installed metadata/dependency/custom packages; do not generate against unreleased APIs that are absent from the target UDE. Retrieval must be bounded and authorized for the chosen assistant. Source does not replace the compiler or UDE runtime, and neither corpus has been inspected during this interview.

## Proposed workflow

1. Read-only preflight and task clarification.
2. Discover current objects, extension points, relations, dependencies, ownership, and relevant examples.
3. Propose implementation objects and target models, including any model/dependency changes, and obtain local-plan approval.
4. Create/update metadata through supported APIs, verify read-back, and compile.
5. Generate SysTest and Playwright tests, runbooks, data prerequisites, and evidence contracts.
6. Deliver the compiled change and generated artifacts on the task branch, clearly identifying deferred runtime work.
7. When requested, obtain deployment/sync approval, verify the exact artifact reaches the selected UDE, and collect logs.
8. Obtain required test/data-effect approvals, execute SysTest and hand off browser tests/runbooks as applicable.
9. Collect assertions, screenshots, step outcomes, retained-record manifests, and cleanup instructions.
10. Sanitize and commit approved evidence; request separate approval for remote push/PR.

This ordering recognizes that UDE runtime tests of new code require deployment before execution. It does not assume test code can run against cloud data before its binaries are deployed.

## Recommended phased milestones

The user approved phasing; this specific sequence is a recommendation:

1. Requirements/design, reference-source inventory, and tool/permission/compatibility probes.
2. Standard table/form extension as the first complete vertical workflow.
3. New custom data/UI/security features and CoC/events.
4. Batch processing and entity/service integration workflows.

For product qualification, demonstrate the advertised runtime workflows on supported installations even though individual deliveries may defer runtime testing.

## Deferred implementation discovery

The frontmatter questions are intentionally open. Resolve them during journey/specification and targeted capability probes; do not invent local paths, version compatibility, repository permissions, UI selectors, data access, or rollback guarantees.

Keep numeric performance targets and repair budgets explicit in the future specification. Recommended behavior is bounded local repair and state reconciliation before any retry of an unknown/failed cloud mutation.

## Recommended next step

The journey is captured in `..\ux\xpp-agentic-development-journey.md`. Use its stable stage/persona IDs when writing the PRD and acceptance criteria.
