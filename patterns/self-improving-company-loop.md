# Pattern: Self-Improving Company Loop

## Definition

A self-improving company loop is a business process that can observe reality, decide what to do, use tools, pass quality gates, and feed failures back into future behavior.

## The Loop

```text
Sensor -> Policy -> Tool -> Quality Gate -> Learning -> Sensor
```

## 1. Sensor

Sensors capture business reality:

- customer tickets;
- product usage;
- cancellations;
- sales conversations;
- code changes;
- incident logs;
- support outcomes.

## 2. Policy

Policy defines what the system may do:

- what can be automated;
- what requires approval;
- what must be logged;
- what is out of scope;
- who is accountable.

## 3. Tool

Tools should be deterministic when possible:

- database queries;
- CRM updates;
- ticket creation;
- test runners;
- code search;
- report generation;
- feature-flag or experiment systems.

## 4. Quality Gate

Quality gates prevent the loop from amplifying errors:

- evals;
- tests;
- lint checks;
- security filters;
- human approval;
- canary rollout;
- rollback plan.

## 5. Learning

Learning means updating durable assets:

- instructions;
- examples;
- tools;
- schemas;
- indexes;
- dashboards;
- playbooks;
- escalation rules.

## Strong Version

The strong version is not "AI does everything." The strong version is "every repeated failure teaches the system something durable."

## Implementation Order

1. Start with read-only observation.
2. Add recommendation generation.
3. Add human-reviewed tool creation.
4. Add low-risk automation.
5. Add higher-risk automation only with strong quality gates.

## Anti-Pattern

An AI loop without policy and quality gates is just a fast way to make mistakes repeatedly.
