# Pattern: Context Engineering

## Definition

Context engineering is the discipline of making the right business facts available to an agent at the right time, with enough structure for the agent to act usefully and enough boundaries to keep the system safe.

## Problem

Agents often produce generic or low-quality output because they lack business context. They know how to follow instructions, but they do not know:

- what customers actually said;
- what the product really does;
- what tradeoffs the team has already made;
- which failures matter;
- which constraints are non-negotiable.

## Useful Inputs

- Customer calls and approved transcripts.
- Support tickets and resolution notes.
- Product telemetry and failure data.
- Team decisions and architecture notes.
- Roadmap constraints.
- Existing PRDs and postmortems.
- Database facts exposed through safe read-only tools.

## Minimum Workflow

1. Define the task: PRD, support response, bug triage, content draft, roadmap synthesis.
2. Identify the required evidence sources.
3. Retrieve source snippets or records with links back to origin.
4. Ask the agent to separate evidence, inference, and recommendation.
5. Run a review step against missing context and overclaiming.
6. Store the final artifact and link it back to the evidence.

## Good Output Looks Like

- Every claim can point to a source.
- Uncertainty is visible.
- Customer-specific and generally reusable requirements are separated.
- The agent explains what it did not know.
- A human can audit the trail.

## Failure Modes

- Dumping too much raw context into a prompt.
- Letting private or sensitive material leak into broad agent access.
- Treating the loudest customer feedback as the whole market.
- Confusing a summary with a decision.
- Losing source links when turning evidence into recommendations.

## Safe Default

Start with read-only context and human-reviewed outputs. Add write actions only after the context, tests, and audit trail are reliable.
