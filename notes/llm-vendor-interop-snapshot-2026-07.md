# LLM Vendor Interop Snapshot: Codex, Claude Code, Plugins, MCP

Verified as of: 2026-07-06

## Summary

As of this snapshot, no official OpenAI/Codex plugin was found for directly calling Claude or Claude Code as a model backend, and no official Anthropic plugin was found for directly calling Codex as a model backend.

The official interoperability layer is better understood as protocol and workflow composition:

- plugins and skills;
- MCP servers;
- CLI / non-interactive execution;
- SDKs;
- hooks and configuration files.

This is not a weakness in the ecosystem. It reflects a product boundary: each vendor's agent harness is deeply tied to its own models, tools, permissions, and support surface.

## OpenAI / Codex Evidence

OpenAI's Codex plugin documentation describes plugins as bundles of skills, app integrations, and MCP servers. The examples include Codex Security, Gmail, Google Drive, Slack, and Sites, not a Claude model bridge:

- Codex plugins: <https://developers.openai.com/codex/plugins>

OpenAI's Codex MCP documentation frames MCP as a way to give Codex access to third-party tools and context:

- Codex MCP: <https://developers.openai.com/codex/mcp>

Interpretation: Codex's official extension direction is reusable workflows and external tools/context, not direct competitor-agent model routing.

## Anthropic / Claude Code Evidence

Claude Code documentation emphasizes MCP, project instructions, skills, hooks, multiple Claude agents, background agents, and the Agent SDK:

- Claude Code overview: <https://docs.anthropic.com/en/docs/claude-code/overview>
- Claude Agent SDK: <https://docs.anthropic.com/en/docs/claude-code/sdk>

Anthropic's gateway documentation says it does not support routing Claude Code to non-Claude models through any gateway:

- Claude Code LLM gateway: <https://docs.anthropic.com/en/docs/claude-code/llm-gateway>

Interpretation: Claude Code's official extensibility is also centered on tools, context, and orchestration around Claude Code, not direct routing to competitor models.

## Practical Implication

Cross-vendor agent collaboration is currently a product engineering responsibility.

Common options:

1. **CLI wrapper**
   - Example shape: call a second agent through non-interactive CLI mode.
   - Good for reviews, second opinions, and isolated critiques.
   - Weaknesses: auth state, model names, output parsing, timeouts, permissions, and session semantics.

2. **MCP server**
   - Use when a system needs a structured tool boundary.
   - Better for productized, auditable integrations.
   - Still requires careful permission design.

3. **SDK integration**
   - Use when the agent loop becomes a product feature.
   - Gives more control over orchestration, permissions, observability, and fallback behavior.

4. **Own orchestrator**
   - Best for multi-model products.
   - The orchestrator owns task routing, evals, logging, policy, cost, and review gates.

## Design Lesson

The lack of a direct official bridge reinforces a broader agent-product lesson:

```text
Model vendor tools are not a complete product architecture.
The application must own orchestration, policy, context, evals, and trust.
```

## Boundary

This is a time-sensitive snapshot. Vendor plugin ecosystems can change quickly. Re-check official docs and local marketplace listings before using this note for current product decisions.
