# Anthropic Code with Claude 2026 Agent Workshops

Source collection:
- https://claude.com/code-with-claude
- https://claude.com/code-with-claude/san-francisco
- https://github.com/anthropics/cwc-workshops

Publisher: Anthropic
Published: 2026
Accessed: 2026-07-07

## Summary

This source note traces a common social-media framing: "Anthropic dropped five
free workshops on building self-improving agentic systems from scratch."

The underlying source is not a standalone Anthropic course with that title. It
is a group of public sessions, recordings, and workshop code from Anthropic's
Code with Claude 2026 developer event. The official material is better treated
as a practical learning path for managed agents, memory, autonomy, proactive
workflows, and decomposition into tools, skills, and subagents.

## Primary Learning Path

| Order | Social-media label | Official title | Source links |
| --- | --- | --- | --- |
| 1 | Ship your first Claude agent | Ship your first Managed Agent | [Session](https://claude.com/code-with-claude/session/ldn-ext-ship-your-first-managed-agent), [YouTube](https://www.youtube.com/watch?v=19HDQ9HppOA), [Code](https://github.com/anthropics/cwc-workshops/tree/main/ship-your-first-managed-agent) |
| 2 | Build memory for Claude agents | Agents that remember | [Session](https://claude.com/code-with-claude/session/ldn-ext-agents-that-remember), [YouTube](https://www.youtube.com/watch?v=geUv4CjPpxI), [Code](https://github.com/anthropics/cwc-workshops/tree/main/agents-that-remember) |
| 3 | Make your agent autonomous | Beyond the basics with Claude Code | [Session](https://claude.com/code-with-claude/session/ldn-beyond-the-basics-with-claude-code), [YouTube](https://www.youtube.com/watch?v=tuY2ChJIx48) |
| 4 | Set up a proactive workflow | Stop babysitting your agents | [Session](https://claude.com/code-with-claude/session/ldn-stop-babysitting-your-agents), [YouTube](https://www.youtube.com/watch?v=wI0ptqCSL0I) |
| 4b | Set up a proactive workflow | Build a proactive agent workflow with Claude Code | [Session](https://claude.com/code-with-claude/session/ldn-build-a-proactive-agent-workflow-with-claude-code), [YouTube](https://www.youtube.com/watch?v=eSP7PLTXNy8) |
| 5 | Self-improving agents: tools, skills | Tool, skill, or subagent? Decomposing an agent that outgrew its prompt | [YouTube](https://www.youtube.com/watch?v=mWvtOHlZM-I), [Code](https://github.com/anthropics/cwc-workshops/tree/main/agent-decomposition) |

## Related Session

- Memory and dreaming for self-learning agents:
  [Session](https://claude.com/code-with-claude/session/sf-memory-and-dreaming-for-self-learning-agents),
  [YouTube](https://www.youtube.com/watch?v=RtywqDFBYnQ)

## Analysis

The useful pattern is not "prompt engineering upgraded." It is a systems stack:

1. a task harness that can run real work;
2. durable memory that captures facts, procedures, and feedback;
3. autonomy boundaries that let the agent act without constant babysitting;
4. proactive workflows that notice when work should happen;
5. decomposition into tools, skills, and subagents when the prompt becomes too
   large or too vague to remain the control surface.

That maps cleanly to the repo's existing thesis on self-improving company
loops. The compounding asset is not a single agent prompt. It is the operating
surface around the agent: source-of-truth context, repeatable tools, memory
updates, review gates, and decomposition discipline.

## Applications

For an AI-native company, treat this material as a build order:

1. Start with a managed agent that can complete one bounded task.
2. Add memory only after the task repeats enough to reveal what should persist.
3. Add autonomy after the read/write boundary, approval policy, and rollback
   story are explicit.
4. Add proactive scheduling only for workflows where the trigger condition is
   observable and the failure mode is acceptable.
5. Split into tools, skills, and subagents when the agent repeatedly carries
   too much implicit procedure in its prompt.

## Source Notes

- Some reposts list only four stages, but the common "five workshops" framing
  includes the tools / skills / subagents decomposition session.
- "Self-improving agentic systems" is a useful shorthand, not the official
  title of the Anthropic source material.
- Prefer the official workshop repository and session pages over reposted
  timestamp threads when implementing against this material.
