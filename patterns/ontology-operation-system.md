# Pattern: Ontology Operation System

Accessed / written: 2026-07-08

## Boundary

This is a public, reusable pattern note. It is not the canonical architecture
source for any one company's product roadmap or multi-repository execution
plan.

Project-specific decisions should live in the relevant product repository and
be coordinated through that project's issue / PR workflow. This note keeps only
the public abstraction: low-code business building, ontology-governed actions,
agent supervision, effect logging, and system-of-record posting.

## Summary

An ontology operation system is a front-office operating layer where people and agents work through business objects, actions, policies, and evidence rather than raw ERP forms or generic chat.

It keeps the low-code promise:

- users can add fields, tables, views, workflows, and actions;
- AI can draft or change those structures;
- operators can still work in spreadsheet-like, kanban-like, form-like, and task-center interfaces.

The difference is that the low-code layer edits a governed business ontology, not production database tables directly.

```text
Low-code builder -> Business ontology -> Governed actions -> Audit ledger -> System-of-record posting
```

## Why This Pattern Exists

Many enterprise AI products start as a chat box attached to existing software. That can help with search, summarization, and drafting, but it does not reliably solve operational work.

Real operations require:

- business objects such as customer, order, asset, vehicle, invoice, ticket, shipment, work order, or supplier;
- relationships and lifecycles between those objects;
- actions that can change the business state;
- permissions and approval rules;
- audit trails and rollback or compensation paths;
- a user experience that front-line workers can actually use.

Traditional ERP systems often store the official record, but their user experience is frequently optimized for management reporting and accounting correctness rather than front-line work. A sales person, service operator, or dispatcher should not have to open a heavy ERP screen just to update the next business action.

The ontology operation system separates these concerns:

```text
Front-line work surface -> Ontology action -> Review / policy -> Posting adapter -> ERP / ledger
```

## Core Claim

The useful architecture is not "ERP plus AI chat" and not "low-code database plus agent".

The stronger pattern is:

```text
Ontology-aware low-code workspace
  + governed agent runtime
  + effect ledger
  + system-of-record adapters
```

The user still gets a low-code building experience. But the system interprets each change:

- Is this only a view column?
- Is this a business field?
- Does it affect accounting, inventory, compliance, or permissions?
- Can an agent read it?
- Can an agent write it?
- Who must approve it?
- Which reports, workflows, or tools become stale?
- Does it need to post back to an ERP, CRM, HCM, SCM, or finance system?

## Conversation Arc Behind The Pattern

This pattern came out of a longer architecture review of enterprise AI products and low-code systems. The reasoning evolved in several steps.

### 1. Palantir Reframed The Problem

The strongest Palantir lesson is not "use a better model". Palantir AIP can connect to models from external providers, while the product advantage is the operational layer around the model.

The important ideas are:

- ontology is more than a database schema;
- business objects, logic, actions, and security belong in one operating model;
- agents should act through governed tools and permissions;
- high-risk actions need staging, human review, branching, rollback limits, and audit.

The useful takeaway is:

```text
Model quality matters, but enterprise agent reliability mostly comes from the operating harness.
```

### 2. Salesforce, SAP, And Oracle Show Domain-Bound Ontologies

Salesforce, SAP, and Oracle do not all replicate Palantir's cross-enterprise ontology, but each shows a domain-bounded version of the same pattern:

- Salesforce centers on CRM / Customer 360 objects, metadata, flows, tools, simulator, trace, and observability.
- SAP centers on ERP, procurement, supply chain, HR, business data, knowledge graph, Joule agents, workflow, and governance.
- Oracle centers on Fusion business objects, agent tools, user-token security, testing, monitoring, and human-in-the-loop controls.

The lesson is that agents become practical when they operate on native business objects and inherit the permissions, rules, and lifecycle of the business system.

### 3. Spreadsheet-Like UX Still Matters

Modern collaboration tools and low-code platforms win adoption because they let users manipulate work directly:

- add a field;
- reorder columns;
- create a filtered view;
- build a form;
- drag a kanban stage;
- create an approval flow;
- automate a repeated step.

That experience should not disappear. The mistake would be to replace it with an abstract ontology editor that only architects can understand.

The right interface is:

```text
User edits a table/view/form/task flow
System turns it into a governed ontology change
```

The user feels like they are building a work app. The platform knows whether that change is display-only, operational, financial, permissioned, agent-visible, or system-of-record-bound.

### 4. NocoBase Shows Why Low-Code Can Feel Light

Low-code platforms can feel lightweight when the front end and back end share a metadata plane.

The reusable pattern is:

- collection / field metadata;
- UI schema;
- resource actions;
- access control;
- audit around schema and resource changes;
- registered components instead of one-off generated pages.

The important lesson is not to copy a specific runtime. The lesson is to pre-wire capability channels so users compose registered parts rather than asking the system to invent a new backend every time they drag a component.

### 5. Open-Design Shows The Manifest Pattern

Agent-created artifacts should not jump directly into production systems.

They should first become:

- manifests;
- schemas;
- digests;
- previews;
- provenance records;
- sandboxed artifacts;
- bounded apply requests.

This maps cleanly to enterprise low-code and agent work:

```text
AI draft -> Manifest -> Validate -> Preview -> Review -> Apply -> Ledger
```

### 6. The Architecture Shift: Dual Core

The unconstrained architecture review compared four routes:

```text
A. Use ERPNext/Frappe as the transaction core and build ontology runtime on top.
B. Replace it with Odoo or another mature ERP.
C. Build the whole ERP/business core from scratch.
D. Build a custom ontology operation system as the main experience, and use ERP/finance/inventory systems as background ledgers.
```

The preferred ranking was:

```text
D > A > C > B
```

Why:

- D gives the highest user-experience ceiling while keeping accounting and inventory correctness in mature systems.
- A is a good transition architecture, especially when production already exists.
- C is too expensive early because accounting, tax, inventory, multi-company, multi-currency, reversal, and audit are multi-year systems.
- B often just swaps one ERP constraint for another.

## Recommended Architecture

```text
User / AI Agent
  -> Ontology Operation System
       - Object Registry
       - Relationship Registry
       - Action Registry
       - Surface Builder
       - Workflow Runtime
       - Policy Engine
       - Agent Runtime
       - Effect Ledger
       - Decision Inbox
  -> Integration / Posting Layer
       - Outbox
       - Idempotency
       - Reconciliation
       - Saga / Compensation
       - ERP Adapter
  -> Authority Systems
       - ERP
       - Accounting ledger
       - Inventory ledger
       - Tax / invoice system
       - CRM / HCM / SCM / ITSM
       - Existing spreadsheets or legacy systems
```

## Low-Code Does Not Go Away

This is the most important product point.

The low-code platform is still the main user experience. It just becomes ontology-aware.

The old version:

```text
Drag component -> Create table/field -> Generate CRUD page
```

The stronger version:

```text
Drag component -> Change business ontology -> Generate surface/action/policy/agent tool/posting rule
```

Users should still be able to:

- create a business table;
- add a field;
- build a detail page;
- create a form;
- build a kanban board;
- configure a task queue;
- add an approval step;
- add an action button;
- let AI generate a draft app;
- inspect and edit what AI generated.

But every change is interpreted through policy:

- view-only;
- operational state;
- system-of-record field;
- reporting metric;
- permission rule;
- agent-readable context;
- agent-writable action;
- ledger-bound transaction.

## Governed Slot Runtime

A practical implementation can use a governed slot runtime.

```text
ContractChange
  -> Classifier
  -> Policy decision
  -> Capability registry
  -> Preview
  -> Apply
  -> Effect ledger
  -> Drift / reconciliation
```

### Capability Registry

The registry declares what users and agents are allowed to compose:

- object slots;
- field slots;
- relation slots;
- surface slots;
- action slots;
- permission slots;
- workflow slots;
- metric slots;
- agent tool slots.

The user does not directly mutate production tables. The user instantiates registered capabilities.

### Risk-Based Routing

Fast path and slow path should be based on risk, not only on whether a slot exists.

Fast path examples:

- hide or show a view column;
- reorder fields;
- create a read-only view;
- create a non-core custom field;
- add a filter or grouping;
- expose an existing action in a new surface.

Slow path examples:

- accounting or inventory state changes;
- permission model changes;
- cross-company access changes;
- workflow state-machine changes;
- agent write permission;
- new system-of-record posting behavior.

## Effect Ledger

The effect ledger is the spine of the system.

It records events such as:

```text
ActionRequested
PolicyEvaluated
PreviewGenerated
HumanApproved
AgentShadowWriteCreated
ERPPostRequested
ERPPostSucceeded
ERPPostFailed
CompensationApplied
Reconciled
```

It supports:

- audit;
- explainability;
- shadow writes;
- replay;
- drift detection;
- idempotency;
- compensation;
- agent supervision.

For higher-risk enterprise work, this should be closer to event sourcing than a simple activity log. The system should be able to reconstruct what happened, not merely store a final status.

## Agent Runtime

Agents should not inherit broad UI or database permissions.

They should receive explicit grants:

```text
AgentGrant
  agent_id
  principal_user_id
  object_scope
  action_scope
  store_or_company_scope
  allowed_modes
  row_or_amount_budget
  time_to_live
  approval_policy
```

Recommended modes:

- read;
- suggest;
- draft;
- shadow write;
- attended write;
- autonomous write for low-risk bounded actions only.

The agent should operate through capability tools, not raw SQL or arbitrary system APIs.

## User Experience Principles

The first screen should not be an ERP form or a generic low-code component marketplace.

It should show:

- work to do;
- business objects;
- exceptions;
- evidence;
- recommended actions;
- approvals;
- impact previews;
- what the agent is doing;
- what changed after an action.

The builder experience should be available when users need to change the work system, but day-to-day operators should mostly see business work, not configuration plumbing.

## Implementation Path

### 90 Days

Prove the dual-core loop, not a full platform.

Build:

- ontology manifest v0;
- effect ledger v0;
- decision inbox v0;
- one object pack;
- one low-code surface builder slice;
- one AI shadow action;
- one system-of-record posting adapter.

Avoid:

- full ERP rewrite;
- universal workflow builder;
- autonomous high-risk writes;
- generic marketplace positioning.

### 6 Months

Make the ontology workspace the primary user experience.

Build:

- table / detail / kanban / task-center surfaces;
- policy engine;
- workflow runtime for bounded cases;
- agent grants;
- reconciliation center;
- versioned industry packs.

Avoid:

- letting users edit ledger-critical schema freely;
- letting agents write into high-risk systems without approvals;
- treating ERP forms as the main workflow.

### 18 Months

Turn the system into a real operation platform.

Build:

- multiple system-of-record adapters;
- industry ontology packs;
- agent action packs;
- implementation compiler;
- cross-system reconciliation;
- evals and ROI dashboards.

Only consider self-building deeper ledger components if adapters become the bottleneck and the domain economics justify it.

## Anti-Patterns

- Adding an AI chat box to ERP and calling it an agent.
- Letting low-code users directly mutate production financial or inventory tables.
- Treating ontology as a static knowledge graph without actions.
- Treating actions as raw API calls without policy and audit.
- Making the first user experience an ontology editor.
- Replacing a painful ERP UI with a painful schema builder.
- Rebuilding accounting and inventory too early.
- Letting agents act through service accounts with broad permissions.
- Storing final answers without evidence, approvals, and effect traces.

## Good Output Looks Like

- A business user can build or modify a working surface.
- The system knows what kind of business change that surface implies.
- The agent can propose actions against business objects.
- High-risk actions are previewed and approved.
- The official system of record receives narrow, governed postings.
- Every important change has evidence, policy, actor, preview, result, and reconciliation status.

## Related Patterns

- [Context Engineering](context-engineering.md)
- [Internal Agent That Evolves](internal-agent-that-evolves.md)
- [Self-Improving Company Loop](self-improving-company-loop.md)
- [Vertical Execution-Layer Agent](vertical-execution-layer-agent.md)
- [Agent Quality Is Harness Engineering](../analysis/agent-quality-is-harness-engineering.md)
- [Product-Layer Agents Without Owning Foundation Models](../analysis/product-layer-agents-without-owning-models.md)

## Source Trail

This note synthesizes product and architecture research from public sources and open-source projects. It is not a transcript and does not reproduce private implementation details.

Primary public references:

- [Palantir Supported LLMs](https://www.palantir.com/docs/foundry/aip/supported-llms/)
- [Palantir Ontology System](https://www.palantir.com/docs/foundry/architecture-center/ontology-system/)
- [Palantir Why create an Ontology?](https://www.palantir.com/docs/foundry/ontology/why-ontology/)
- [Palantir AIP Logic Blocks](https://www.palantir.com/docs/foundry/logic/blocks/)
- [Palantir Global Branching](https://www.palantir.com/docs/foundry/global-branching/overview/)
- [Palantir Action Reverts](https://www.palantir.com/docs/foundry/action-types/action-reverts/)
- [Palantir: Reducing Hallucinations with the Ontology in AIP](https://blog.palantir.com/reducing-hallucinations-with-the-ontology-in-palantir-aip-288552477383)
- [Palantir: Connecting Agents to Decisions](https://blog.palantir.com/connecting-agents-to-decisions-277dee8ddb40)
- [NocoBase documentation](https://docs.nocobase.com/)
- [Open Design](https://github.com/nexu-io/open-design)
- [ERPNext / Frappe DocTypes](https://docs.frappe.io/framework/user/en/basics/doctypes)
- [Odoo ORM documentation](https://www.odoo.com/documentation/19.0/developer/reference/backend/orm.html)
- [Temporal use cases and design patterns](https://docs.temporal.io/evaluate/use-cases-design-patterns)
- [Twenty open-source CRM](https://github.com/twentyhq/twenty)

## Boundary

This pattern is most useful for operational domains where front-line work, business state, agent actions, and system-of-record correctness all matter. For lightweight internal tools, a simpler low-code database or workflow automation product may be enough.
