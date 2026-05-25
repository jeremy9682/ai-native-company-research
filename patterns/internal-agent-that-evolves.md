# Pattern: Internal Agent That Evolves

## Definition

An internal agent evolves when repeated tasks become durable tools, feedback becomes durable instructions, and business facts become durable context.

## Core Components

- **Task queue**: Slack, email, support, CRM, and scheduled jobs become explicit tasks.
- **Thin harness**: a small wrapper around a coding-capable agent or CLI.
- **Read-only business context**: codebase, database replicas, docs, and logs.
- **Tool interfaces**: deterministic CLIs or APIs for business systems.
- **Tool builder**: a coding agent that can create new tools under review.
- **Instruction memory**: an editable behavior file loaded on each run.

## Three Memories

1. **Factual memory**: how the product and business currently work.
2. **Behavioral memory**: how the team wants the agent to act.
3. **Procedural memory**: how recurring tasks are performed.

## Improvement Loop

1. Agent receives a task.
2. Agent attempts it using existing context and tools.
3. If the task recurs and no tool exists, the system proposes a new tool.
4. Tool is generated, reviewed, tested, and logged.
5. Future tasks use the tool instead of ad hoc reasoning.
6. Human feedback updates instructions and examples.

## Governance Requirements

- New tools need tests.
- Tool changes need review.
- Sensitive tools need permission boundaries.
- Instruction changes need a changelog.
- Production-impacting actions need approval or rollback.

## What To Automate First

- Status queries.
- Drafting and summarization.
- CRM updates with human confirmation.
- Support triage.
- Internal report generation.
- Monitoring jobs.

## What To Delay

- Direct production writes.
- Billing changes.
- Customer-facing commitments.
- Security changes.
- Personnel or performance decisions.

## Practical Test

If the agent completes a task once, that is productivity. If it creates a reviewed tool or instruction that makes the next similar task easier, that is compounding.
