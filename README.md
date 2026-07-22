# AI-Native Company Research

Public research notes on how AI-native companies are being built: context engineering, internal agents, self-improving operating loops, and practical company-brain patterns.

This repository is for **analysis and reusable frameworks**, not for reposting copyrighted material. It links to original sources, summarizes them, and extracts implementation patterns.

## Start Here

- [Content Policy](CONTENT_POLICY.md) — what can and cannot be included.
- [YC Root Access: AI-native company series](sources/yc-root-access/2026-05-19-ai-native-company-series.md) — first source analysis.
- [Y Combinator: AI-native company and GStack](sources/y-combinator/2026-04-ai-native-company-and-gstack.md) — company operating model plus agentic engineering workflow.
- [Anthropic Code with Claude 2026 Agent Workshops](sources/anthropic/2026-code-with-claude-agent-workshops.md) — managed agents, memory, autonomy, proactive workflows, and decomposition.
- [Context Engineering](patterns/context-engineering.md) — why agents need company context.
- [Internal Agent That Evolves](patterns/internal-agent-that-evolves.md) — how tools and memories compound.
- [Self-Improving Company Loop](patterns/self-improving-company-loop.md) — sensor/policy/tool/quality/learning loop.
- [Evidence Timeline Agent Blueprint](blueprints/evidence-timeline-agent.md) — safe public blueprint for customer-evidence-driven product work.
- [Agent Quality Is Harness Engineering](analysis/agent-quality-is-harness-engineering.md) — why model access and framework integration are not enough.
- [Product-Layer Agents Without Owning Foundation Models](analysis/product-layer-agents-without-owning-models.md) — how agent products can compound above third-party models.
- [Vertical Execution-Layer Agent](patterns/vertical-execution-layer-agent.md) — an auditable agent layer next to a system of record.
- [Ontology Operation System](patterns/ontology-operation-system.md) — low-code business building plus ontology-governed agents and system-of-record posting.
- [LLM Vendor Interop Snapshot: Codex, Claude Code, Plugins, MCP](notes/llm-vendor-interop-snapshot-2026-07.md) — verified-as-of 2026-07 notes on official interoperability surfaces.
- [Anthropic: Large-Scale Code Migrations with Claude Code](sources/anthropic/2026-07-16-large-scale-code-migrations.md) — six-step migration playbook (judge, rulebook, stress test, loop burning) plus a Frappe/ERPNext-to-Go worked example.

## Collected Sources

| Date | Video ID | Source | Speaker / Author | Organization | Public note |
|---|---|---|---|---|---|
| 2026-04-23 | `wkv2ifxPpF8` | [How to Make Claude Code Your AI Engineering Team](https://www.youtube.com/watch?v=wkv2ifxPpF8) | Garry Tan | Y Combinator / GStack | [Analysis](sources/y-combinator/2026-04-ai-native-company-and-gstack.md) |
| 2026-04-24 | `EN7frwQIbKc` | [How To Build A Company With AI From The Ground Up](https://www.youtube.com/watch?v=EN7frwQIbKc) | Diana Hu | Y Combinator | [Analysis](sources/y-combinator/2026-04-ai-native-company-and-gstack.md) |
| 2026-05-19 | `DGD9b8K42lk` | [How to Build an Internal AI Agent That Evolves Itself](https://www.youtube.com/watch?v=DGD9b8K42lk) | Ayush Garg | AnswerThis / YC Root Access | [Analysis](sources/yc-root-access/2026-05-19-ai-native-company-series.md) |
| 2026-05-19 | `t-G67yKAHBQ` | [How to Build a Self-Improving Company with AI](https://www.youtube.com/watch?v=t-G67yKAHBQ) | Tom Blomfield | YC / YC Root Access | [Analysis](sources/yc-root-access/2026-05-19-ai-native-company-series.md) |
| 2026-05-19 | `1egwM88T3C0` | [How to Give AI Agents Enough Context to Be Useful](https://www.youtube.com/watch?v=1egwM88T3C0) | Suchintan Singh | Skyvern / YC Root Access | [Analysis](sources/yc-root-access/2026-05-19-ai-native-company-series.md) |
| 2026 | multiple | [Code with Claude 2026 Agent Workshops](https://claude.com/code-with-claude) | Anthropic | Anthropic | [Analysis](sources/anthropic/2026-code-with-claude-agent-workshops.md) |
| 2026-07-16 | — | [How Anthropic runs large-scale code migrations with Claude Code](https://claude.com/blog/ai-code-migration) | Anthropic (feat. Jarred Sumner, Mike Krieger) | Anthropic | [Analysis](sources/anthropic/2026-07-16-large-scale-code-migrations.md) |

This public list tracks what has already been summarized here. Match future YouTube links by `Video ID`; tracking parameters after the ID do not make a new source. Complete transcripts, raw captions, video, and audio are intentionally not stored in this public repository.

## What This Repo Is

This is a public knowledge base for founders, builders, and operators exploring how companies change when AI agents become part of the operating system.

It focuses on:

- turning company context into useful agent memory;
- turning repeated work into durable tools;
- keeping human responsibility clear;
- using quality gates before automation reaches production;
- separating reusable company knowledge from disposable internal software.

## What This Repo Is Not

This repo does **not** host:

- full YouTube transcripts;
- downloaded video or audio;
- unlicensed slide screenshots;
- private customer data;
- internal company chats;
- financial, investor, or confidential strategy material.

## Current Thesis

AI-native companies will not just add copilots to existing workflows. The useful pattern is a loop:

1. capture relevant business evidence;
2. make it legible to agents;
3. expose deterministic tools;
4. add quality gates and human responsibility;
5. feed failures back into tools, instructions, and process.

The durable asset is not a dashboard. It is the company context, evidence, rules, tools, and review discipline that let better dashboards and workflows be generated repeatedly.

## Source Standard

Each source note should include:

- original URL;
- publisher / speaker;
- publication date when available;
- access date;
- summary in original words;
- analysis separated from source summary;
- no large verbatim excerpts unless the source license explicitly allows it.

## License

Original analysis in this repository is shared under [CC BY 4.0](LICENSE.md), unless a file says otherwise. Third-party source materials remain owned by their original authors and publishers.
