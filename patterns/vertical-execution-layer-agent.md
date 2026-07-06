# Pattern: Vertical Execution-Layer Agent

## Definition

A vertical execution-layer agent sits next to a system of record and turns domain work into auditable, reviewable, and repeatable work units.

It does not replace the system of record. It helps users act on top of it.

```text
System of Record -> Domain Context -> Work Unit -> Tool Action -> Approval -> Audit -> Learning
```

## Problem

Generic agents can draft, summarize, and plan. But business workflows usually require more:

- domain-specific objects;
- permissions;
- approvals;
- error handling;
- rollback;
- traceability;
- repeatable implementation steps;
- evidence that a recommendation is grounded in reality.

In operational domains such as manufacturing, logistics, insurance, healthcare administration, finance operations, or ERP-heavy SMB work, a plausible answer is not enough. The system must be inspectable.

## Core Components

### 1. Provider Layer

The model provider should be replaceable infrastructure:

- task-level model routing;
- fallback models;
- cost and latency tracking;
- quality monitoring;
- provider-specific quirks hidden behind a stable interface.

The provider layer is necessary, but it is not the product moat.

### 2. Domain Context Engine

The agent needs structured business context:

- object schemas;
- relationships between objects;
- historical outcomes;
- approved rules and templates;
- source evidence;
- user and permission context;
- current workflow state.

This extends the [Context Engineering](context-engineering.md) pattern into a vertical domain.

### 3. Work Units

Long tasks should be broken into work units that humans and systems can inspect:

- input;
- evidence;
- proposed action;
- risk level;
- approval requirement;
- execution status;
- verification result;
- learning link.

Work units make agent behavior reviewable and repeatable.

### 4. Deterministic Tools First

When a task can be expressed as a deterministic tool, prefer the tool.

The agent should:

- identify missing information;
- draft candidate actions;
- explain tradeoffs;
- propose new tools or rules;
- call approved tools under policy.

Accepted recurring behavior should become tools, templates, or evals rather than staying as free-form prompting.

### 5. Human Approval

Approval is part of the product surface, not an afterthought.

High-risk actions should require human confirmation:

- customer-facing commitments;
- pricing changes;
- financial operations;
- production data writes;
- security or permission changes;
- irreversible workflow transitions.

The user should be able to approve, reject, edit, or escalate.

### 6. Audit And Rollback

The system should record:

- source evidence;
- model and prompt version;
- tool calls;
- intermediate outputs;
- human approvals;
- final action;
- verification result.

Where rollback is possible, it should be explicit. Where rollback is not possible, the system should show the recovery path.

### 7. Evaluation Loop

The vertical agent needs domain evals:

- extraction accuracy;
- workflow completion;
- approval pass rate;
- error rate;
- time saved;
- customer or operator outcome;
- regression after model or prompt changes.

Owning evals is especially important when the product does not own the foundation model.

## Minimum Data Model

```text
WorkUnit
  id
  domain
  title
  status
  source_links
  evidence_summary
  proposed_action
  risk_level
  approval_required
  tool_calls
  execution_result
  verification_status
  learning_link
```

## Implementation Order

1. Start with read-only context and draft recommendations.
2. Add work-unit creation and human review.
3. Add deterministic tools for low-risk repeated actions.
4. Add audit logs and regression evals.
5. Automate low-risk actions after approval quality is consistent.
6. Expand to higher-risk actions only with stronger gates and rollback.

## Good Output Looks Like

- The user can see why the agent recommended an action.
- The evidence trail is inspectable.
- The action is bounded by policy.
- The system records what happened.
- A failure improves future tools, rules, or evals.

## Anti-Patterns

- Selling "multi-model support" as the product.
- Letting the agent write to production without a policy surface.
- Treating approval as a modal afterthought.
- Storing only final answers and losing the evidence path.
- Relying on prompt changes instead of building tools and evals.
- Building a horizontal agent platform when the domain workflow is the asset.

## Related Patterns

- [Context Engineering](context-engineering.md)
- [Internal Agent That Evolves](internal-agent-that-evolves.md)
- [Self-Improving Company Loop](self-improving-company-loop.md)
- [Evidence Timeline Agent Blueprint](../blueprints/evidence-timeline-agent.md)
- [Agent Quality Is Harness Engineering](../analysis/agent-quality-is-harness-engineering.md)
- [Product-Layer Agents Without Owning Foundation Models](../analysis/product-layer-agents-without-owning-models.md)

## Boundary

This pattern is most useful when the domain has meaningful operational risk. For low-risk creative drafting or one-off research, a lighter agent workflow may be enough.
