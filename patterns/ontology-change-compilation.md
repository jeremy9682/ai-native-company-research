# Pattern: Ontology Change Compilation

Accessed / written: 2026-07-15

## Boundary

This is a public, reusable pattern note. It does not describe any private deployment, customer system, product roadmap, or production instance.

Examples use generic low-code and ERP concepts. The note focuses on one seam inside an ontology operation system: compiling a logical contract change into consistent physical effects. For the broader architecture, see [Ontology Operation System](ontology-operation-system.md).

## Summary

In an ontology-first low-code system, adding a field is not merely a UI operation or database alteration. It is a contract change that may affect:

- projections and user interfaces;
- permissions and confidentiality;
- actions and agent tools;
- system-of-record bindings;
- data migration and recovery;
- audit and reconciliation.

The load-bearing problem is change compilation:

```text
Contract change
  -> Classify its meaning
  -> Resolve physical bindings
  -> Generate every required target change
  -> Preview and apply
  -> Verify against the authoritative system
  -> Record the result
```

The hardest boundary appears when the compiler reaches a real ERP or other system of record.

At that boundary, two different failure classes must be separated:

1. **Mapping-layer gaps:** the logical model is sufficient, but the adapter has an incomplete or ambiguous view of the physical system.
2. **Model-level insufficiency:** the logical action abstraction cannot express the real lifecycle, even with perfect mappings.

The first class is fixed with better inventory and exact bindings. The second requires a richer object and action model: multiple physical resources, composite identity, typed multi-aggregate operations, and explicit transaction or compensation semantics.

## Why This Pattern Exists

A low-code interface makes schema changes appear local:

```text
User adds field -> New column appears
```

But a governed operational system has more consumers than the visible surface:

```text
                         -> Projection / UI
                         -> Permission policy
User adds field -> Change compiler -> Action contracts
                         -> System-of-record adapter
                         -> Migration / rollback
                         -> Audit / reconciliation
```

If any target is omitted, the system can become internally inconsistent:

- the form displays a field that actions cannot write;
- an agent tool accepts a value that the ERP adapter drops;
- a confidential field inherits a public projection;
- a migration creates storage without a recovery path;
- the ontology reports success before the authoritative record changes;
- audit records the request but not the physical effect.

Generating each target independently does not solve the problem. The outputs must come from one classified change plan so that they agree on meaning, identity, permissions, and failure behavior.

## Core Claim

An ontology-aware low-code platform needs a change compiler, not a collection of schema callbacks.

The compiler must perform three jobs:

```text
1. Classification
   What kind of change is this?

2. Propagation
   Which contracts, surfaces, policies, resources, and migrations must change?

3. Authority verification
   Did the change actually hold in the system of record?
```

A successful in-memory update proves only that the internal model is self-consistent. It does not prove that the adapter discovered the right physical resource, created real storage, preserved permissions, executed a real action, or recovered safely from partial failure.

## The Change Compiler Contract

### 1. Classify Before Generating

A field-like change may be:

- view-only;
- a derived metric;
- evidence or provenance;
- workflow state;
- record-bound;
- an action input;
- confidential.

These classifications are not necessarily mutually exclusive. A record-bound field can also be confidential and accepted as an action input. Classification should therefore produce obligations, not merely select a display type.

```text
FieldChange
  semantic_roles
  storage_requirement
  read_policy
  write_policy
  action_exposure
  authority_binding
  migration_requirement
  audit_requirement
```

The compiler should reject unresolved meaning. Guessing that every field is ordinary editable storage is unsafe, while treating every field as view-only makes the system appear integrated without proving a write path.

### 2. Propagate Through One Plan

The compiler should emit a versioned change plan before applying effects:

```text
ChangePlan
  change_id
  source_contract_version
  target_contract_version
  classifications
  logical_bindings
  physical_bindings
  projection_changes
  permission_changes
  action_changes
  adapter_changes
  migration_steps
  rollback_or_compensation
  audit_events
  verification_checks
```

Each target should have an explicit result:

```text
planned | unchanged-by-design | unsupported | applied | verified | failed
```

Silence is not a valid result. If an adapter cannot support a record-bound field or multi-resource action, the compiler should return a typed unsupported result instead of degrading to an in-memory or view-only change.

### 3. Verify From the Authority

Verification must read back from the system that owns the official state.

```text
Compile success != Apply success
Apply success   != Authority verification
```

A useful verification step checks:

- the physical schema or extension exists;
- the expected identity resolves;
- the runtime principal can perform the permitted operation;
- the authoritative record contains the expected value;
- unauthorized projections remain hidden;
- the audit entry refers to the actual physical effect;
- rollback or compensation remains available where promised.

## The Centerpiece: Two Failure Classes At The Real-System Boundary

Mapping gaps and model insufficiency can produce similar symptoms. Both may look like “the ERP adapter failed.” Their remedies are fundamentally different.

## Failure Class A: Mapping-Layer Gaps

A mapping-layer gap means the logical operation is expressive enough, but the adapter does not know exactly where or how to apply it.

Common examples include:

- a permission-trimmed schema response omits fields or object types;
- default pagination returns only the first page of records;
- the same flat field name appears on many physical object types;
- a document identifier behaves like a primary key but is not exposed as an ordinary declared field.

A naive adapter may build this index:

```text
status -> "status"
name   -> "name"
amount -> "amount"
```

That index becomes ambiguous as soon as several object types expose the same names.

The required binding is object-scoped:

```text
LogicalFieldRef
  logical_object
  logical_field

PhysicalFieldBinding
  physical_object_type
  physical_column
  physical_identity
  read_capability
  write_capability
  visibility_scope
  metadata_version
```

The mapping should be exact:

```text
(logical_object, logical_field)
  -> (physical_object_type, physical_column)
```

A complete inventory is equally important. The adapter should:

- enumerate every object type within its approved integration scope;
- paginate until exhaustion;
- inspect metadata under the intended and authorized discovery context;
- distinguish “not visible” from “does not exist”;
- record identity conventions separately from ordinary fields;
- retain object type in every field reference;
- version the inventory so drift can be detected.

This does not authorize bypassing system permissions. It prevents the compiler from treating a permission-limited response as a complete description of the underlying model.

### Mapping-Layer Test

Ask:

> If the adapter had a complete inventory and an exact object-scoped binding, could the existing logical action express the operation correctly?

If yes, the problem is probably mapping.

The fix belongs in discovery, identity resolution, namespace handling, or capability metadata. It does not require redesigning the ontology.

## Failure Class B: Model-Level Insufficiency

A model-level insufficiency remains even after every physical table, column, key, and permission is mapped correctly.

The common cause is an action abstraction that assumes:

```text
One logical object
  -> One physical object
  -> One aggregate
  -> One field-level mutation
```

Real business lifecycles do not always fit that shape.

### Cross-Table State Movement

Consider a generic inventory item that leaves a stock table when sold. The physical operation may be:

```text
Read stock row
  -> Validate availability
  -> Create sold-item row
  -> Create or update transaction row
  -> Remove or close stock row
  -> Emit audit / downstream event
```

This is not necessarily equivalent to:

```text
stock.status = "sold"
```

The state transition is represented by movement across resources. No more precise field mapping can turn a cross-table migration into a single-field update.

### Composite Logical Entities

One logical entity may also be represented by several physical tables sharing a composite identity:

```text
Logical process instance
  -> Header table       keyed by (organization, process_id)
  -> Stage table        keyed by (organization, process_id, stage)
  -> Evidence table     keyed by (organization, process_id, evidence_id)
  -> Outcome table      keyed by (organization, process_id, revision)
```

A single-object adapter can read one fragment and still miss the lifecycle invariant carried by the other fragments.

### What The Model Must Add

The logical object needs a multi-resource binding:

```text
LogicalObjectBinding
  logical_object
  physical_resources[]
  composite_identity
  load_plan
  consistency_rules
  lifecycle_rules
```

The action needs a typed operation plan:

```text
OperationPlan
  operation_type
  input_contract
  preconditions[]
  reads[]
  steps[]
  postconditions[]
  idempotency_key
  atomicity_scope
  compensation_steps[]
  verification_plan
```

Each step should name its physical resource and operation:

```text
OperationStep
  resource_binding
  operation_kind
  key_binding
  input_binding
  expected_version
  failure_policy
```

The adapter then chooses the appropriate execution guarantee:

```text
One transactional authority
  -> Local database transaction

Several independent authorities
  -> Durable workflow / saga
  -> Idempotent steps
  -> Explicit compensation
  -> Reconciliation
```

An outbox may be used when a committed local change must reliably propagate an event without an unsafe dual write.

### Model-Level Test

Ask:

> Does correctness require coordinated effects across multiple physical resources, or does the logical identity itself span those resources?

If yes, the problem is not a missing map. The action model is too narrow.

The single-resource action can remain as an optimized special case, but the public action contract should be able to represent a sequence from the start. Otherwise, the first real multi-table lifecycle forces a breaking redesign of actions, audit events, retries, and rollback semantics at the same time.

## Why The Distinction Matters

Treating a mapping problem as a model problem creates unnecessary abstraction.

Treating a model problem as a mapping problem is more dangerous. It produces increasingly elaborate adapter code that still cannot state the business invariant.

```text
Mapping problem:
  "Which physical field represents this logical field?"

Model problem:
  "Which coordinated operation preserves this logical lifecycle?"
```

Both can occur in the same integration. A complete inventory may first reveal the correct tables and then expose that the current action cannot coordinate them.

## Sequence: In Memory, Then Real, Then Scored

The most effective proof sequence is:

```text
In-memory seam proof
  -> Real isolated system-of-record slice
  -> Early user-value check
  -> Formal regression harness
```

This ordering is not an argument against testing. It is an argument for testing the right abstraction only after reality has challenged it.

## Stage 1: Prove The Compiler Seam In Memory

The in-memory implementation should prove that one contract change can:

- be classified;
- produce a complete change plan;
- update projections;
- update permissions;
- update action contracts;
- generate adapter intent;
- generate migration and recovery intent;
- generate audit events;
- reject unsupported combinations.

This stage makes iteration fast and exposes missing compiler targets.

It does not validate the ERP boundary.

## Stage 2: Prove One Unambiguous Real Slice

Next, use a real but isolated and desensitized system of record. It should preserve the actual metadata, API, identity, permission, transaction, and failure behavior without exposing private records.

The slice should contain:

1. one real stored extension field;
2. one real action that crosses the adapter boundary;
3. one authoritative read-back;
4. one migration and rollback or compensation path;
5. one end-to-end audit trail.

The field must be physically stored. A virtual or derived field can make the adapter a no-op:

```text
Add virtual field
  -> Internal projection changes
  -> ERP schema remains unchanged
  -> Adapter reports nothing to do
```

That may be correct behavior for a derived metric, but it proves nothing about schema migration or record-bound storage.

A stronger slice is:

```text
User adds stored extension field
  -> Compiler classifies it
  -> Preview lists every affected target
  -> Migration creates real storage
  -> Projection and permissions update
  -> Real action writes through the adapter
  -> Authority read-back verifies the value
  -> Audit links request, plan, effect, and verification
  -> Recovery path is exercised
```

Using one narrow slice keeps failure attribution clear. If the first proof combines many objects, actions, and workflows, it becomes difficult to tell whether a failure came from classification, mapping, permissions, identity, migration, or transaction behavior.

## Stage 3: Build The Formal Regression Harness

After the real slice has exposed the boundary, convert its lessons into durable tests:

- classification fixtures;
- target-completeness checks;
- inventory and pagination cases;
- namespace-collision cases;
- synthetic or implicit identity cases;
- multi-resource operation-plan tests;
- migration and rollback tests;
- idempotency and compensation tests;
- authoritative read-back checks;
- permission and confidentiality regressions.

A heavy scored harness built before the real slice can be internally impressive while validating only an invented world.

```text
Many passing simulated cases
  + No real stored field
  + No real authoritative action
  = High confidence in an unproven boundary
```

The real slice discovers what deserves to become an evaluation case. The harness then protects that knowledge from regression. This follows the broader principle in [Agent Quality Is Harness Engineering](../analysis/agent-quality-is-harness-engineering.md), while adding an important sequencing constraint: the harness must be grounded in the real operational seam it claims to evaluate.

## Category Discipline: Compiler Proof Is Not Product Proof

Technical validation and product validation answer different questions.

| Claim | Required evidence |
|---|---|
| The compiler propagates a contract change correctly | Target plan, real adapter effect, authoritative read-back, migration recovery, and audit |
| The workflow is usable | A real user can understand the preview, perform the action, and recover from mistakes |
| The product creates value | A real user completes meaningful work more effectively than with the prior workflow |

Passing the first row does not prove the second or third.

A rigorous compiler harness can manufacture the impression that “the framework is validated” because it produces precise scores, traces, and coverage. Those artifacts are valuable only for the technical claim they measure.

The lean real slice should therefore reach a real user early. The user does not need to evaluate the architecture. The user needs to attempt real work through the slice:

```text
Can the user understand the field?
Can the user complete the action?
Does the result appear where the user expects?
Can the user recognize and recover from failure?
Is the workflow materially useful?
```

The compiler protects operational correctness. User validation tests whether the operation was worth compiling.

## Migration And Recovery Are Compiler Outputs

A record-bound change is incomplete without a transition plan.

For compatible evolution, the compiler can use an expand-migrate-contract sequence:

```text
Expand
  -> Add new storage and compatible readers

Migrate
  -> Backfill or dual-handle old and new representations
  -> Verify counts, identities, and invariants

Contract
  -> Remove obsolete paths only after verification
```

Rollback is not always equivalent to reversing every write. Once users or external systems depend on a new field, removing it may destroy information or break consumers. The plan should state whether recovery means:

- schema rollback;
- data restore;
- disabling new writes;
- retaining but hiding the field;
- forward repair;
- compensating business actions;
- manual reconciliation.

The same distinction applies to actions. A local transaction may roll back. A distributed operation often needs compensation rather than pretending the world can be restored to an earlier snapshot.

## Compiler Invariants

A production-worthy change compiler should preserve these invariants:

- **Target completeness:** every required target is planned or explicitly unsupported.
- **No silent degradation:** a record-bound change cannot quietly become view-only.
- **Object-scoped identity:** physical fields are never resolved by bare name alone.
- **Authority-aware verification:** success is checked in the official system, not only in a cache.
- **Permission preservation:** discovery, preview, execution, and projection respect their declared authorization scopes.
- **Migration safety:** stored changes include transition and recovery behavior.
- **Typed atomicity:** operation plans declare whether they rely on a local transaction, saga, compensation, or reconciliation.
- **Idempotent retry:** retried actions do not duplicate physical effects.
- **Audit continuity:** the original request, compiled plan, approvals, physical effects, and verification share one traceable identity.
- **Drift visibility:** changes in physical metadata invalidate or recheck stale bindings.

## Anti-Patterns

- Treating “add field” as a UI-only callback.
- Using a virtual or derived field to claim that schema migration works.
- Building a large scored harness before executing one real authoritative slice.
- Assuming one permission-scoped schema response is a complete inventory.
- Reading only the first page of object or record discovery.
- Indexing fields by bare name across many object types.
- Assuming every document identifier is an ordinary declared field.
- Encoding a cross-table lifecycle as a field setter.
- Hiding multi-resource writes inside untyped adapter code.
- Reporting success after an internal write without authoritative read-back.
- Treating compensation as an implementation detail added after actions ship.
- Treating compiler correctness as evidence of user or market value.
- Allowing unsupported targets to become silent no-ops.

## Good Output Looks Like

- One user change produces one inspectable, versioned change plan.
- Classification explains why every target is affected.
- Physical bindings include object type, column, identity, capability, and metadata version.
- Complete inventory prevents pagination, permission, and namespace mistakes.
- Mapping failures and model failures have different typed errors.
- A stored extension field is created in a real isolated system of record.
- A real action changes authoritative state and is verified by reading it back.
- Multi-resource lifecycles compile into typed operation plans.
- Transactions, retries, idempotency, compensation, and reconciliation are explicit.
- Migration and recovery are part of the generated artifact.
- Audit connects intent to physical effect.
- Formal evaluations encode failures first discovered at the real boundary.
- A real user validates that the lean slice helps complete meaningful work.

## Related Patterns

- [Ontology Operation System](ontology-operation-system.md) — the broader ontology, low-code, action, audit, and system-of-record architecture.
- [Vertical Execution-Layer Agent](vertical-execution-layer-agent.md) — the surrounding pattern for domain work, governed tools, approval, and audit.
- [Agent Quality Is Harness Engineering](../analysis/agent-quality-is-harness-engineering.md) — why reliable agent behavior depends on contracts, runtime, gates, and evaluation.
- [Context Engineering](context-engineering.md) — how systems select and structure the context needed for reliable action.

## Source Trail

This note is original synthesis. The public sources below document related building blocks; they do not make the specific two-failure-class distinction or prescribe the sequencing presented here.

Public source summaries:

- [Palantir Global Branching](https://www.palantir.com/docs/foundry/global-branching/overview/) documents isolated branches for proposed changes before they reach a shared environment.
- [Palantir Action Reverts](https://www.palantir.com/docs/foundry/action-types/action-reverts/) documents action reversion and its limits, including cases where object edits can be reverted but external side effects are not.
- [Frappe Framework: Understanding DocTypes](https://docs.frappe.io/framework/user/en/basics/doctypes) documents metadata-backed object types whose definitions drive storage and generated application surfaces.
- [Frappe Framework REST API](https://docs.frappe.io/framework/user/en/api/rest) documents resource access, field selection, and pagination, including a default list response that returns a limited number of records and only their names.
- [Frappe Framework: Naming](https://docs.frappe.io/framework/user/en/basics/doctypes/naming) documents the special `name` primary key and multiple document naming strategies.
- [Frappe Framework: Users and Permissions](https://docs.frappe.io/framework/user/en/basics/users-and-permissions) documents object, role, field-level, and restricted-view permissions.
- [Temporal: Saga Design Pattern](https://temporal.io/blog/saga-pattern-made-easy) explains multi-step operations that track forward progress and use explicit compensation when partial execution is undesirable.
- [Debezium Outbox Event Router](https://debezium.io/documentation/reference/stable/transformations/outbox-event-router.html) documents reliable propagation from committed local state through an outbox.
- [Parallel Change](https://martinfowler.com/bliki/ParallelChange.html) describes the expand-migrate-contract approach for evolving interfaces and database structures incrementally.

The interpretation in this note is that these mechanisms become parts of one change-compilation boundary: exact mapping solves incomplete physical knowledge, while multi-resource binding and typed operation plans solve logical expressiveness.
