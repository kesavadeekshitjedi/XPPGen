# X++ object creation and agentic development in Visual Studio 2022

Status: evidence-based technical review and requirements/design input. Product decisions below were established through the requirements interview. Implementation discovery items remain explicitly open; recommendations are labeled and are not automatically approved decisions.

Reviewed on 2026-10-14 against [XppAiCopilotCompanion](https://github.com/nazmolla/XppAiCopilotCompanion), branch `master`, commit `909d822c3c211bf3bef704f2da81c4ec4eb82070`.

## 1. Executive assessment

The repository provides a useful foundation: a Visual Studio extension, a local MCP server, a bridge into the installed Dynamics 365 Finance and Operations development tools, and structured metadata operations. It does **not** currently implement the requested conversation-to-code-to-compilation-to-tests-to-UDE-deployment lifecycle.

The distinction between an advertised object, a dispatched operation, and a tested business scenario matters. The current MCP create schema and bridge dispatcher support 19 base object types. The update dispatcher implements nine types, not all 19. Metadata extension types appear in the UI and mapping layer but are rejected by the creation dispatcher. A schema generated for a type does not mean the server can create that type.

The most important missing capabilities are:

- Actual table/form and other metadata-extension creation.
- Reliable per-operation capability reporting and complete structured results.
- Compiler invocation and structured diagnostic collection.
- UDE deployment and database-synchronization orchestration with explicit approvals.
- SysTest discovery/execution and result collection.
- Browser acceptance runbooks and evidence contracts.
- Durable operation state, recovery, target identity, and proof of completion.

This is a static source review, not a certification of runtime behavior. No VSIX was installed, no X++ compiler was run, no application metadata was changed, and no UDE was contacted. Upstream statements such as "fully tested" and "18 of 19" are claims, not independently reproduced results in this review.

## 2. Decisions established so far

| Topic | User decision |
|---|---|
| Primary persona | Intermediate X++ developer or technical consultant using the agent daily for normal Windows/VS2022 development, with review time. |
| Principal user friction | Discover whether functionality exists or needs an extension, implement performantly, and deploy/test; writing code is comparatively straightforward. |
| Primary failure to prevent | Redundant, incorrect, or slow business behavior despite successful compilation, including unintended UDE side effects. |
| Workflow breadth | Standard table/form changes, CoC/events, new custom data/UI/security objects, batch processing, entities/services, and concrete customer requests. This is an epic. |
| Local authority | After approval of the implementation plan, approved local edits and builds can run automatically. |
| Cloud authority | Explicit approval before UDE deployment/database synchronization and tests that write business data. |
| Existing data | Ground scenarios in existing UDE data. Provide user instructions or a portable Playwright instructions file to create missing prerequisite data. |
| Browser evidence | Capture screenshots and save the output of every data-creation/save step. |
| Test layers | Both X++ SysTest and browser acceptance scenarios/evidence. |
| Conversation host | Copilot Chat in VS2022, including Claude models if the subscription supports them; a separate Claude extension/client is not required. |
| Installation baseline | Assume a working VS2022/D365FO/UDE setup; check it with a read-only preflight rather than provisioning it. |
| Writable models | The approved implementation plan may include creating custom models/packages and changing dependencies; Microsoft and unowned ISV models remain reference-only. |
| Delivery sequencing | Phased delivery, retaining all requested workflow families in the final scope. |
| Data mutations | Test records by default; separate approval for existing-record changes, posting, external effects, and batch effects. |
| Browser deliverables | Executable Playwright TypeScript tests plus a human-readable runbook, for the user or their chosen capable tool to run. |
| Test-record disposition | Retain tagged browser-created records with an identifiers manifest and cleanup runbook; use SysTest isolation/rollback where appropriate. |
| Source-controlled outputs | Code, tests, scripts, documents, screenshots, and sanitized run evidence belong together in source control. |
| Evidence commits | Automatic sanitized evidence commits under an approved repository/data policy; always exclude credentials and reusable authentication state. |
| Git delivery | Task branch with automatic approved commits; explicit approval for push/PR; no auto-merge or direct shared-default-branch writes. |
| Unsupported automation | Guided manual VS action with evidence collection; clearly distinguish manual, pending, and blocked steps from automated success. |
| Implementation foundation | Start from the GitHub companion's VSIX + MCP + in-process Dynamics metadata bridge design; resolve source-reuse rights and compatibility before incorporating implementation code. |
| Per-request delivery threshold | Successful compilation and generated tests/runbooks are enough; runtime testing may be deferred and must be labeled unverified. |

Implementation discovery remains open: actual application/tool versions, subscription/model availability, approved repository/data policy, permitted environment/company identities, concrete writable models, source availability, license/reuse rights, supported automation adapters, and quantitative performance/retention limits. These are prerequisites for implementation or execution, not invented decisions.

## 3. What runs where

```text
Developer / conversation host
    -> MCP client
    -> XppMcpServer.exe (local HTTP, normally port 21329)
    -> MetaModelBridgeServer inside Visual Studio (normally port 21330)
    -> installed D365FO MetaModel/project services on the VS UI thread
    -> local custom metadata

Local X++ compiler and build tools
    -> compiled custom packages
    -> explicitly approved deployment/synchronization
    -> connected UDE cloud runtime
    -> SysTest and browser execution against cloud business data
```

Microsoft's UDE documentation explicitly separates the local development tier from the cloud execution tier. Source creation and compilation occur locally. Execution against UDE data requires the relevant binaries to have been deployed to that environment.

The reference metadata, compiler, Dynamics tools, and target environment need compatible versions. The downloaded cross-reference database supports discovery; it is not the UDE business database.

### Repository implementation boundaries

| Component | Actual responsibility |
|---|---|
| `mcp-server/ToolRouter.cs` | Advertises 16 MCP tools, validates some arguments, routes requests, and formats responses as text. |
| `MetaModel/MetaModelBridgeServer.cs` | Parses JSON into requests, dispatches to the VS UI thread, and runs post-write metadata validation. |
| `MetaModel/MetaModelBridge.cs` | Calls installed Dynamics metadata services to create/read/update objects and manage project references. |
| `MetaModel/MetaModelContracts.cs` | Request/result DTOs and convenience metadata structures. |
| `MetaModel/Generated/MetadataProxies.g.cs` | Generated metadata representations used by the typed-graph conversion path; generated breadth is not operation coverage. |
| `CopilotLanguageModelService.cs` | Composes prompts. The direct language-model call is a stub returning `null`, so it falls back to a manual chat prompt. |
| `CopilotContextProviderAdapter.cs` | Adapter methods for a prospective host integration; not proof of universal context injection into every chat request. |
| `XppCopilotPackage.cs` | Starts/registers the MCP server and writes VS-style `.mcp.json` configuration. |
| Replication audit scripts | Environment-dependent metadata smoke/round-trip checks, not compiler, SysTest, or business-process acceptance tests. |

## 4. A reliable object-creation protocol

Use the actual MCP schemas exposed by the installed server. The following is a workflow description, not a claim that these tools were invoked during this review.

1. Discover the environment, active model, reference packages, project, and available capabilities.
2. Locate relevant existing objects using bounded, type-filtered discovery.
3. Read a representative object and inspect the exact metadata shape. Follow actual relations rather than guessing joins from names.
4. Confirm ownership, explicit target model/package, required dependencies, naming prefix, labels, and extension strategy.
5. Obtain the runtime type schema when necessary. Verify that creation and update are implemented separately for that type.
6. Present an object plan in dependency order and get developer approval.
7. Create via the metadata API using JSON and plain X++ source strings. Do not generate raw metadata XML.
8. Read back and compare the intended metadata and source. Explicitly add to the project if needed and verify project membership.
9. Compile using the installed X++ build toolchain and collect errors, warnings, and best-practice diagnostics.
10. Obtain approval for deployment/synchronization, deploy the exact compiled artifact, and check cloud operation logs.
11. Execute approved SysTest and browser scenarios; record assertions, environment/company identity, and evidence.

Creating an object successfully is only step seven. It is not proof of a valid form, working entity, correct permissions, compilation success, or cloud availability.

### Declaration versus methods

For class-like objects, send the class declaration/member declarations separately from complete method source strings. The current update implementation upserts methods by a name extracted from the source and optionally removes named methods.

CoC extensions, event handlers, runnable classes, SysOperation classes, contracts, and SysTest classes are generally **class patterns**, not additional MCP metadata types. They use `AxClass`, with the appropriate inheritance, attributes, signatures, and methods verified against the target application's APIs.

Formatting in the bridge normalizes tabs, line endings, and trailing whitespace. It is not an X++ parser, compiler, or full formatter. Compilation must catch malformed source and incorrect signatures.

### Convenience metadata versus full typed graphs

Convenience parameters include `properties`, `enumValues`, `fields`, `indexes`, `fieldGroups`, `relations`, `entryPoints`, and query `dataSources`. They cover selected shapes, not every metadata feature.

`typedMetadata` supports richer graphs, such as nested form structures, where the runtime types and mapping support them. In the current metadata application path it takes precedence over the convenience structures. Avoid supplying overlapping representations and assuming they merge.

The generic typed-graph writer clears supplied collections before repopulating them. Treat collection payloads as replacement-oriented, not automatically additive patches. Read the current state, preserve unrelated children, and verify the complete intended collection after writing.

Unknown or unwritable properties can be skipped by the reflection path. An accepted payload is not proof that every field was applied. Verify expected values explicitly and require actionable errors for unsupported graph nodes in a production implementation.

### Illustrative JSON payloads

These are examples of the current create-argument shape, not executed tool calls or certified application code. `ApprovedCustomModel` must be replaced with a verified, explicitly approved existing target model. A naming prefix and labels must follow the actual repository conventions.

Minimal class with declaration and methods separated:

```json
{
  "objectType": "AxClass",
  "objectName": "DemoValueHelper",
  "modelName": "ApprovedCustomModel",
  "declaration": "public final class DemoValueHelper\n{\n}",
  "methods": [
    "public static int twice(int _value)\n{\n    return _value * 2;\n}"
  ]
}
```

Minimal enum:

```json
{
  "objectType": "AxEnum",
  "objectName": "DemoReviewStatus",
  "modelName": "ApprovedCustomModel",
  "enumValues": [
    { "name": "Draft", "value": 0 },
    { "name": "Reviewed", "value": 1 }
  ]
}
```

Small custom-table structure using convenience fields:

```json
{
  "objectType": "AxTable",
  "objectName": "DemoReviewRecord",
  "modelName": "ApprovedCustomModel",
  "fields": [
    { "name": "Description", "fieldType": "String" },
    { "name": "SequenceNumber", "fieldType": "Int" }
  ],
  "indexes": [
    {
      "name": "SequenceIdx",
      "allowDuplicates": false,
      "fields": ["SequenceNumber"]
    }
  ],
  "fieldGroups": [
    { "name": "AutoReport", "fields": ["SequenceNumber", "Description"] }
  ]
}
```

For a real table, select the appropriate EDTs, labels, lengths, keys, table properties, relations, and company behavior before creation. These minimal shapes deliberately do not claim to be production-complete metadata. Discover complex form/entity/security graphs from the installed type schema and read-back of relevant examples rather than fabricating nested JSON keys.

For labels, inspect the approved label file with `xpp_read_label` and create/update required label entries with `xpp_create_label`. Those tools do not establish that creating an entirely new label file is supported. Add that capability separately or provide a supported manual step when the project lacks a suitable label file.

## 5. Base-object creation catalog

All entries below describe dispatched source paths, not runtime-certified coverage.

| Object type | How the current bridge creates it | Metadata needed for a useful object | Update support in current dispatcher |
|---|---|---|---|
| `AxClass` | `CreateClass`, then shared update path | Declaration, complete methods, verified inheritance/attributes and dependencies | Yes |
| `AxTable` | `CreateTable`, then shared update path | Fields/EDTs, indexes, keys, relations, field groups, table properties, methods | Yes |
| `AxView` | `CreateView`, then shared update path | Data sources, joins, projected fields, computed-field definitions where applicable | Yes |
| `AxDataEntityView` | `UpdateDataEntityView` creates the initial object, then shared update path | Entity data sources, field mappings, keys, public/data-management settings and methods where supported | Yes, but the convenience update path does not apply the separate `methods` array |
| `AxEdt` | Always instantiates `AxEdtString` | String subtype settings, labels/help text, length and applicable references | Yes; creating non-string EDT subtypes needs additional implementation |
| `AxEnum` | `CreateEnum`, then shared update path | Values, labels, extensibility and verified value semantics | Yes |
| `AxForm` | `CreateForm`, then shared update path | Data sources, design/control hierarchy, bindings, patterns, labels, source methods | Yes; a blank form is not a usable maintenance form |
| `AxMenu` | `CreateMenu`, then shared update path | Submenus and correctly typed menu-item references | Yes |
| `AxMenuItemDisplay` | Applies metadata before `CreateMenuItemDisplay` | Form target, label, parameters and appropriate permissions | No |
| `AxMenuItemAction` | Applies metadata before `CreateMenuItemAction` | Runnable action target and settings, label and permissions | No |
| `AxMenuItemOutput` | Applies metadata before `CreateMenuItemOutput` | Report/output target and settings, label and permissions | No; it does not create the report itself |
| `AxQuery` | Always instantiates `AxQuerySimple`, then shared update path | Root/nested data sources, joins, links, ranges and field selection | Yes in dispatcher; upstream reports complex-metadata issues |
| `AxSecurityPrivilege` | Applies metadata before `CreateSecurityPrivilege` | Entry points and applicable access details | No |
| `AxSecurityDuty` | Applies metadata before `CreateSecurityDuty` | Correct privilege references, labels and duty structure | No |
| `AxSecurityRole` | Applies metadata before `CreateSecurityRole` | Duty/privilege membership and appropriate role structure | No |
| `AxService` | Applies metadata before `CreateService` | Implementing class, operations and verified contract/signature metadata | No |
| `AxServiceGroup` | Applies metadata before `CreateServiceGroup` | Service membership and applicable deployment settings | No |
| `AxConfigurationKey` | Applies metadata before `CreateConfigurationKey` | Parent/label/settings verified against the runtime schema | No |
| `AxTile` | Applies metadata before `CreateTile` | Target menu item, display/count settings and context | No |

### Data objects: creation order and checks

For new custom data features, reuse existing EDTs/enums when appropriate; otherwise create shared types before tables. Add fields, indexes, groups, and relations before properties that refer to them.

For every relation, verify the actual foreign-key/related-key mapping, cardinality, company behavior, and deletion semantics. Inspect date-effective metadata and select the appropriate temporal behavior rather than mechanically adding date filters.

Table changes can require cloud database synchronization after deployment. Never assume a successful local metadata write has created the corresponding cloud database column.

For views and entities, discover the typed graph from a matching example. The table convenience `fields` parameter is not a universal field-mapping contract. Test keys, joins, null behavior, company scope, and exposed operations against the intended functional requirement.

### Forms, navigation, and security

A useful custom form requires data-source bindings and a supported design/pattern, not just the form name. Verify nested data-source/control methods where required; the root-level `methods` convenience parameter should not be assumed to address every nested method location.

Create the target form/action/output object before its menu item; then create navigation and security references. Privileges, duties, and roles are different metadata graphs. The existence of an `entryPoints` convenience parameter does not mean entry points are the correct representation of duty/role membership.

Browser acceptance must run as the intended user role where possible. Success under the UDE developer's administrator account does not establish correct end-user authorization.

### Batch and integration patterns

Batch processing is built from the appropriate X++ framework classes and optional menu/security artifacts. Inspect existing SysOperation or other relevant framework implementations. Verify retries, idempotency, company scope, cancellation, volume, and asynchronous side effects.

Services and entities need runtime checks beyond metadata creation: supported signatures/contracts, authorization, exposure, serialization, error behavior, and intended endpoint semantics. Do not infer an endpoint is usable merely because a service group object exists.

## 6. Metadata extensions and unsupported object families

The central extensibility gap is concrete:

- The UI lists table/form/enum/EDT/menu and other extension types.
- `MetaModelObjectCreationService` maps several of these types to their metadata names.
- The MCP create schema excludes them.
- `MetaModelBridge.CreateObject` has no corresponding dispatch cases and returns unsupported-type errors.

Therefore, adding a field/control to standard table/form metadata cannot currently be completed through the shipped create dispatcher. A CoC class can change behavior, but it does not substitute for a table/form metadata extension.

The required approach is to implement the relevant extension types through the installed Dynamics metadata APIs, verify their correct concrete type/schema and target references, and add matching create/read/update/validation/discovery coverage. Until that exists, use the supported Dynamics designer for the unsupported operation; do not fall back to raw metadata XML generation.

Other families advertised in the menu, or relevant to a broader AOT catalog, include composite entities, security policies, reports, workflows, aggregate measurements/dimensions, KPIs, resources, and licensing/configuration variants. They are not supported merely because they appear in an enum or menu prompt. Each needs an installed-version API investigation and its own capability/test matrix.

Some requested concepts are framework patterns rather than independently creatable metadata types. Verify the real AOT/runtime representation before adding a fictional type to the tool catalog.

## 7. Reliability findings that affect the design

| Finding | Evidence | Required consequence |
|---|---|---|
| README overstates update breadth | `UpdateGenericByTypedApi` returns unsupported; only nine types are dispatched | Publish capabilities per operation and concrete subtype. |
| Metadata validation is not compilation | `ValidateObject` reads/comparisons metadata and project information | Separate metadata, build, deploy/sync, and runtime gates. |
| Project validation can accept same-model objects | `InProject = inProjectByItems || inProjectByModel` | Verify exact project membership, not only model identity or name substrings. |
| Create does not explicitly call `AddToProject` in the reviewed path | Separate `AddToProject` API exists; wrapper reports `AddedToProject = true` | Verify any Dynamics-service side effect rather than trusting the wrapper flag. |
| Validation can be lost at the MCP boundary | Bridge serializes `validation`; router's generic formatter does not append it | Return machine-readable operation and validation results; fail the gate on mismatch. |
| Direct menu AI generation is not implemented | `TryCallLanguageModel` returns `null` | Use a verified chat-host MCP integration or implement a supported host adapter. |
| Full typed graphs replace supplied collections | `ApplyObjectNode` clears and repopulates collections | Preserve unrelated children and detect stale-state conflicts. |
| Creation can leave a blank object after metadata failure | Create saves first, then updates; failure message acknowledges blank object | Report partial state, reconcile before retry, and provide approved cleanup. |
| Explicit model resolution can fall back to active model | `GetModelSaveInfo` uses active project after a named-model miss | Reject unknown explicit targets; do not silently change placement. |
| Ownership indicators are not a complete ownership policy | `IsCustomModel` uses model-name substrings; reviewed write paths lack an explicit ownership check | Enforce an explicit writable-model/owner policy in the backend; do not rely on prompt text or `isCustom`. |
| Timeout does not prove cancellation | Bridge reports that the UI-thread operation may still be running | Reconcile actual object state before retrying a timed-out mutation. |
| Build setup is not self-contained | VSIX project references locally extracted Dynamics assemblies under `Samples` | Document prerequisites and version pins; do not claim a clean-clone build was verified. |

The architecture document describes holding a dispatch gate until a timed-out task completes. The reviewed code instead releases the gate in `finally` and tracks/drains the previous task on later dispatch. Evaluate actual behavior, not the historical description, when designing recovery.

The repository's committed MCP transcript even names a delete tool that is absent from the current tool list/router. Treat transcripts, prompts, comments, and historical documentation as secondary to the current executable contract.

## 8. Proposed agent lifecycle

This is a recommendation, not a completed implementation.

```text
Clarify -> Discover -> Plan -> Approve local change scope
    -> Create/update -> Read-back validation -> Compile -> Repair bounded failures
    -> Propose SysTest + browser scenarios + data prerequisites
    -> Approve UDE target/deployment/synchronization
    -> Deploy -> Verify cloud operation and artifact identity
    -> Approve data-writing test scope
    -> Execute SysTest / hand off browser runbook
    -> Collect assertions and evidence -> Report verified/failed/blocked outcomes
```

Testing the new code in UDE necessarily requires a pre-test deployment. This should be distinguished from any final handoff/deployment after tests pass; do not design a sequence that promises cloud execution before the new binaries exist there.

A local build may have automatic online deployment enabled in the Dynamics project/model options. Under the selected approval policy, the agent must inspect those settings and prevent an apparently local build from silently deploying or synchronizing.

### Proposed capability additions

These are conceptual interfaces, not existing MCP tool names:

| Capability | Required inputs | Required evidence |
|---|---|---|
| Preflight | VS session, model/package, application/tool versions, environment identity | Compatibility, writable scope, connection status, build/deploy settings |
| Build | Approved scope and exact source state | Compiler result, structured diagnostics, logs, produced artifact identity |
| Deploy/sync | Approved environment, artifact/package set, explicit sync choice | Operation ID, completion status, downloaded logs, verification |
| SysTest | Deployed test identities, selection, company/data assumptions, approval if writing | Discovered/executed counts, individual results, failures, logs |
| Browser runbook generation | Scenario, target environment/company, prerequisites, approval boundaries | Portable steps, expected assertions, screenshot/result artifact plan |
| Recovery | Previous operation ID and recorded source/cloud state | Reconciled status; no blind duplicate writes or false success |

Keep the metadata backend independent of the conversation provider. Provider choice should not change how compilation results, permissions, approvals, or deployment identity are checked.

The approved plan may include new custom models/packages and dependency changes. The current 16-tool surface has model discovery but no advertised model/package creation or dependency-edit tool. Those operations need a verified adapter or a clearly labeled supported manual VS step. List their intended ownership, package placement, references, and test-model relationship before plan approval.

### Selected chat-host contract

Use Copilot Chat in VS2022 Agent mode. A Claude model, when enabled by the developer's subscription and organizational policy, is a model selection within that host, not a second IDE integration.

Microsoft's MCP documentation specifies VS2022 17.14 with the latest servicing release recommended. Verify the actual model picker, Agent mode, tool discovery, and native tool invocation on the supported installation. Do not hard-code a Claude model name or promise availability based only on a subscription label.

The backend approval policy remains necessary even when the host offers broad or remembered tool permissions. Chat-host permission to call a tool is not a substitute for approval of a specific UDE deployment or business-data mutation.

## 9. Browser data-setup and acceptance evidence

The user's requested browser artifact should be portable across tools capable of launching Playwright. Its exact format remains open; a natural-language runbook and executable Playwright script have different portability guarantees.

Recommended runbook contents:

- Scenario purpose and explicit expected business result.
- Target environment and legal entity; expected role and language.
- Existing records to use, selection criteria, and missing-prerequisite handling.
- Every create/save step, input values, expected persisted record, and evidence filename.
- Assertions for positive, negative, boundary, and regression cases.
- A screenshot and structured outcome after each creation/save checkpoint.
- A final persisted-state verification, not just a screenshot of an unsaved form.
- Stop conditions, cleanup/retention instructions, and a record of mutations.

Suggested per-step result fields: run ID, scenario/step ID, timestamp, environment/company, action, expected outcome, observed outcome, pass/fail/blocked status, created record identifiers, screenshot path, and relevant messages.

Authentication should remain interactive through the approved browser/session mechanism. Do not put passwords, tokens, or reusable authentication state in generated runbooks or committed artifacts.

Use semantic navigation and validated controls; avoid hard-coded timing and invented selectors. Stop if the environment/company, permissions, prerequisite records, or UI differ from expectations. A screenshot is supporting evidence, not a replacement for an assertion or persisted-state check.

Screenshots and logs can expose customer/business data. Storage, redaction, access, retention, and whether artifacts may be committed still require a user decision.

## 10. Would ApplicationSuite or FinOps source in msdyneng help?

Yes. It would improve:

- Selection of valid API signatures and real framework implementations.
- Discovery of actual standard-object extension points and attributes.
- Table fields, relations, keys, company scope, and date-effective behavior.
- Form patterns, navigation and security relationships.
- Existing SysTest patterns and realistic functional scenarios.
- Dependency and impact analysis grounded in the installed application.

It is not sufficient alone. The ideal reference set is the matching installed application metadata, ApplicationPlatform/Foundation and other dependency packages, custom/ISV metadata with ownership information, model descriptors, compatible compiler/tools, and a current cross-reference database.

FinOps engineering source in msdyneng could add implementation context, framework conventions, and test examples beyond the particular ApplicationSuite snapshot available on a developer machine. Those benefits depend on what the actual repository contains; its contents and access have not been verified here.

Use that source in two complementary ways:

- Design-time review: derive framework-specific generation guidance, metadata recipes, extension strategies, test patterns, and representative evaluation scenarios from relevant code and tests.
- Generation-time retrieval: for each request, locate the relevant objects, follow references, and retrieve only the verified signatures, relations, examples, and tests needed to implement that change.

A one-time review or a large source dump is not enough to make later generations reliably grounded. Preserve the source version and provenance of examples, and check generated changes against the installed toolchain.

An engineering branch may contain unreleased methods or metadata that do not exist in the target UDE. Prefer a release/build-matched snapshot; when it differs, the target installation's supported metadata and APIs govern what generated code can use. Engineering implementations are reference material, not permission to modify standard objects or use internal/non-extensible APIs.

For example, a field-validation request should retrieve the actual table and extension points, related EDT/relation definitions, an applicable validation implementation, and relevant SysTests. It should then create the custom metadata extension and behavior changes through the bridge, compile them against the target version, and generate positive/negative tests. This is more valuable than asking the model to imitate broadly similar code without checking its applicability.

Start with an inventory of versions, model/package names, and local paths rather than uploading the whole source tree. Prefer tool-mediated, bounded retrieval of relevant metadata into the approved assistant context. Source remaining on disk does not mean it never leaves the machine: retrieved context may be sent to the chosen model service, so its authorization and data-handling policy must be verified.

Neither ApplicationSuite source nor the FinOps source in msdyneng has been supplied or inspected in this review. Source grounding should augment the companion architecture, not replace its metadata operations or compiler/runtime validation.

## 11. Delivery status, product qualification, and acceptance

### Per-request delivery status

The user selected successful compilation plus generated tests/runbooks as sufficient for delivery. Runtime testing may be deferred.

Use separate dimensions rather than a single misleading success flag:

| Dimension | Example statuses |
|---|---|
| Implementation | Planned, written, metadata-verified, compiled, blocked |
| Test artifacts | Not generated, generated, reviewed |
| Deployment/synchronization | Not requested, awaiting approval, manual pending, running, verified, failed |
| Runtime tests | Not run, awaiting approval, manual/external execution pending, passed, failed |
| Evidence/source control | Prepared, sanitation blocked, committed, awaiting push approval, published |

A valid delivery statement is: "Implementation compiled; SysTest and Playwright tests/runbooks generated; runtime verification deferred." Do not translate "generated" into "passed" or "compiled" into "deployed." Delivery does not require an approved push, PR, or runtime execution.

### Product qualification

The lower per-request delivery threshold does not prove the product can execute its advertised runtime workflows. Before calling the system runtime-capable, demonstrate each supported workflow against a recorded tool/application version:


| Workflow | Minimum proof |
|---|---|
| Standard field + form + validation | Actual metadata extensions, build success, cloud schema sync, UI persistence, rule enforcement, SysTest and browser evidence |
| CoC/event change | Verified extension point/signature, original behavior retained, targeted positive/negative/regression assertions |
| New custom maintenance feature | Types/table/form/menu/security created in correct model, usable UI, intended-role access |
| Batch processing | Correct company/selection, bounded volume, idempotency/retry/cancellation checks, observable execution |
| Entity/service integration | Valid metadata/contracts, actual runtime operation, authorization and error-case assertions |

Add focused metadata round-trip cases for every supported concrete subtype. Test partial writes, duplicate names, unknown models, unavailable services, timeout reconciliation, graph collection replacement, and missing project membership. Ensure negative tests do not silently count skipped or unexecuted cases as passed.

Recommended recovery policy: bound automatic local repairs within the approved plan; stop and explain when the plan needs new behavior/dependencies or the repair budget is exhausted. Do not automatically repeat failed/unknown cloud writes. Gather logs and reconcile state first. Failed synchronization is not safely reversible by assuming redeployment rolls back database/data changes.

Do not run upstream replication scripts casually in a working model: they create objects, contain environment-specific paths, and are not a substitute for isolated deterministic tests. A model/package is persistent state even when a VS project is temporary.

Runtime validation here remains blocked on a configured VS/Dynamics installation, matching metadata/compiler, and explicitly approved UDE access. This does not prevent documenting verified source behavior, but it limits readiness claims.

## 12. Source references

Repository references are pinned to the reviewed commit:

- [MCP tools and routing](https://github.com/nazmolla/XppAiCopilotCompanion/blob/909d822c3c211bf3bef704f2da81c4ec4eb82070/vsix-companion/mcp-server/ToolRouter.cs): schemas at lines 27-258; routing and response formatting from line 265.
- [Metadata bridge](https://github.com/nazmolla/XppAiCopilotCompanion/blob/909d822c3c211bf3bef704f2da81c4ec4eb82070/vsix-companion/MetaModel/MetaModelBridge.cs): create/update dispatch at lines 76-209; validation from 643; runtime schema from 1159; project add from 1553; creation helpers from 1678; update helpers from 1820; model resolution from 2067; typed graph application from 2416; ownership indicators from 3429.
- [Bridge server](https://github.com/nazmolla/XppAiCopilotCompanion/blob/909d822c3c211bf3bef704f2da81c4ec4eb82070/vsix-companion/MetaModel/MetaModelBridgeServer.cs): create/update handling from 244; result/validation serialization from 650.
- [AI service](https://github.com/nazmolla/XppAiCopilotCompanion/blob/909d822c3c211bf3bef704f2da81c4ec4eb82070/vsix-companion/CopilotLanguageModelService.cs): direct AI call stub and manual-prompt fallback.
- [Creation wrapper](https://github.com/nazmolla/XppAiCopilotCompanion/blob/909d822c3c211bf3bef704f2da81c4ec4eb82070/vsix-companion/MetaModelObjectCreationService.cs): base/extension mappings and reported project status.
- [VS package](https://github.com/nazmolla/XppAiCopilotCompanion/blob/909d822c3c211bf3bef704f2da81c4ec4eb82070/vsix-companion/XppCopilotPackage.cs): MCP registration/configuration.
- [Replication audit](https://github.com/nazmolla/XppAiCopilotCompanion/blob/909d822c3c211bf3bef704f2da81c4ec4eb82070/run-mcp-replication-audit.ps1): metadata-based test harness.
- [Unified developer experience overview](https://learn.microsoft.com/en-us/power-platform/developer/unified-experience/finance-operations-dev-overview).
- [Install and configure development tools](https://learn.microsoft.com/en-us/power-platform/developer/unified-experience/finance-operations-install-config-tools).
- [Write, deploy, and debug X++ code](https://learn.microsoft.com/en-us/power-platform/developer/unified-experience/finance-operations-debug).
- [Unit testing in UDE](https://learn.microsoft.com/en-us/power-platform/developer/unified-experience/finance-operations-testing).
- [MCP servers in Visual Studio](https://learn.microsoft.com/en-us/visualstudio/ide/mcp-servers?view=vs-2022).
- [Custom instructions in Visual Studio](https://learn.microsoft.com/en-us/visualstudio/ide/copilot-chat-context?view=vs-2022).

The inspected repository did not expose a declared license through GitHub metadata. Confirm reuse/distribution rights before incorporating its implementation into a distributable product.

## 13. Delivery phasing and deferred implementation discovery

Phased delivery was selected, without dropping any workflow family. Recommended milestones:

1. Requirements/design and representative capability probes. Establish permission, version, ownership, and source-control contracts.
2. Standard table/form extension through the complete local build and approved UDE/test lifecycle.
3. New custom data/UI/security objects and CoC/events.
4. Batch and entity/service scenarios with their distinct side-effect and integration checks.

The milestone order is a recommendation; specific dates, owners, supported subtype breadth, and release gates need implementation planning.

Deferred discovery:

- Verify Copilot subscription/policies, model picker, Agent mode, VS servicing version, and native tool discovery.
- Inventory application/compiler/Dynamics tools/reference metadata versions and local source availability.
- Identify approved writable models, environment/company targets, dependencies, naming conventions, and label files.
- Start from the companion architecture; resolve source-reuse rights, actual implementation changes, and installed-version compatibility.
- Prove each build/deploy/sync/SysTest adapter; document supported manual steps where necessary.
- Define the approved repository/data policy, screenshot/log sanitation, storage limits, retention, and record tagging/cleanup rules.
- Specify executable Playwright runner configuration, authentication handoff, supported UI variants, and import of external execution results.
- Measure acceptable discovery/build/deployment/test latencies and choose explicit retry/repair budgets.

The agreed decisions and deferred items are captured in `docs\brain-dumps\xpp-agentic-development-brain-dump.md`. The six-stage developer journey is in `docs\ux\xpp-agentic-development-journey.md`. Use its stable stage/persona IDs in the PRD and acceptance criteria; this technical review is supporting evidence, not a replacement for those artifacts.
