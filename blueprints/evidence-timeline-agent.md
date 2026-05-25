# Blueprint: Evidence Timeline Agent

This is a public-safe blueprint for turning customer or product feedback into structured product work without exposing private customer data.

## Goal

Convert scattered signals into a traceable timeline:

```text
Signal -> Evidence -> Requirement -> Decision -> Implementation -> Verification -> Learning
```

## Inputs

- Support ticket metadata.
- Customer-approved call notes.
- Product logs or telemetry summaries.
- Bug reports.
- Sales notes.
- Implementation notes.
- Test and verification results.

## Output

Each case should produce:

- problem statement;
- affected user or segment;
- evidence links;
- reproduction path when applicable;
- impact estimate;
- customer-specific vs reusable classification;
- proposed next action;
- review owner;
- verification result;
- learning added to playbook or tool.

## Why It Matters

AI-generated PRDs and recommendations become much more useful when they are tied to evidence. Without the timeline, the system is just producing persuasive prose. With the timeline, a human can audit the path from customer signal to shipped change.

## Minimum Data Model

```text
Case
  id
  title
  status
  source_type
  source_link
  evidence_summary
  affected_workflow
  reusable_pattern
  recommended_action
  owner
  verification_status
  learning_link
```

## Human Boundaries

The agent may draft:

- summaries;
- PRDs;
- reproduction checklists;
- test plans;
- implementation suggestions;
- weekly reports.

The agent should not independently:

- promise delivery dates to customers;
- change production systems;
- expose private data in public artifacts;
- make personnel or accountability decisions.

## First Version

Start with a read-only agent that generates draft case timelines. Let humans approve the action plan. Only after the evidence quality is consistently strong should the system create tickets, pull requests, or workflow changes automatically.
