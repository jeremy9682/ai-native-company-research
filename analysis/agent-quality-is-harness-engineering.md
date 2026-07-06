# Agent Quality Is Harness Engineering

Accessed / written: 2026-07-06

## Summary

Strong agent products are not created by wiring a model to a framework. The quality gap usually lives in the harness: the product layer that gives a model the right context, tools, runtime, review gates, memory, and evaluation loop.

Open-source frameworks and public prompting methods can accelerate this work, but they do not replace it. They provide ingredients. The hard part is turning those ingredients into a reliable operating surface for real user work.

## Core Claim

Agent quality is mostly a systems problem:

```text
Model -> Context -> Tool Contract -> Runtime -> Quality Gate -> Learning Loop
```

The model matters, but the harness determines whether the model can act reliably across long, messy tasks.

## What The Harness Owns

### 1. Context Selection

The agent needs access to the right facts at the right time:

- user intent;
- project or business constraints;
- source evidence;
- prior decisions;
- current system state;
- tool results;
- safety and permission boundaries.

Too little context makes the output generic. Too much context makes the system noisy, slow, and harder to audit.

### 2. Tool Contracts

Useful agents need tools with clear contracts:

- accepted inputs;
- deterministic outputs when possible;
- permission boundaries;
- validation;
- failure modes;
- rollback or recovery behavior;
- logs that humans can inspect.

The tool layer is where many demo agents fail. A model can propose plausible actions, but a product must decide which actions are allowed, how they run, and how the system recovers when they fail.

### 3. Runtime

Long-running work needs more than a chat loop:

- task state;
- retries;
- partial progress;
- cancellation;
- checkpoints;
- parallel sub-work;
- audit logs;
- resumability.

Without a runtime, an agent is usually limited to short, fragile tasks.

### 4. Quality Gates

Quality gates prevent the system from turning model confidence into operational damage:

- tests;
- evals;
- static checks;
- human review;
- permission prompts;
- canary rollout;
- rollback plans.

The important question is not only "Can the model do this once?" It is "Can the system detect when the model should not act?"

### 5. Learning Loop

A strong agent system turns failures into durable assets:

- better instructions;
- better examples;
- new deterministic tools;
- tighter schemas;
- regression tests;
- evaluation cases;
- playbook updates.

One successful answer is productivity. One reviewed tool or eval that improves the next similar task is compounding.

## Why Framework Integration Is Not Enough

Frameworks can help with orchestration, memory, tool calling, and model routing. They do not automatically provide:

- domain-specific context;
- customer-specific workflows;
- production permissions;
- long-horizon reliability;
- task-specific evaluation data;
- UX that helps users supervise the agent;
- organizational rules about who is accountable.

This is why two products can use similar models and libraries while producing very different user outcomes.

## Practical Checklist

Before calling a system an agent product, ask:

- What state does the task live in?
- What sources can each claim trace back to?
- What tools can the agent call, and what can they change?
- Which actions require approval?
- What happens when a tool call fails halfway?
- How do we know a model or prompt upgrade improved results?
- What does the user see, approve, undo, or escalate?
- Which repeated failures become new evals, tools, or instructions?

## Related Patterns

- [Context Engineering](../patterns/context-engineering.md)
- [Internal Agent That Evolves](../patterns/internal-agent-that-evolves.md)
- [Self-Improving Company Loop](../patterns/self-improving-company-loop.md)
- [Evidence Timeline Agent Blueprint](../blueprints/evidence-timeline-agent.md)

## Boundary

This note is a framework, not a claim that every successful agent company has the same architecture. It is a way to inspect where product quality is likely to come from after the model is chosen.
