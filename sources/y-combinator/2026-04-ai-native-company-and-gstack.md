# Y Combinator: AI-Native Company And GStack

Source links:

- [How to Make Claude Code Your AI Engineering Team](https://www.youtube.com/watch?v=wkv2ifxPpF8)
- [How To Build A Company With AI From The Ground Up](https://www.youtube.com/watch?v=EN7frwQIbKc)

Publisher: Y Combinator  
Published: 2026-04-23 and 2026-04-24  
Accessed: 2026-05-25  
Note: This page is a paraphrased analysis. It does not include full transcripts.

## Source Index

| Video ID | Video | Speaker / Author | Organization |
|---|---|---|---|
| `wkv2ifxPpF8` | How to Make Claude Code Your AI Engineering Team | Garry Tan | Y Combinator / GStack |
| `EN7frwQIbKc` | How To Build A Company With AI From The Ground Up | Diana Hu | Y Combinator |

## Why These Two Sources Belong Together

These videos connect strategy and execution:

1. Diana Hu explains the company-level operating model: AI as the operating system, closed loops, queryable organization, software factories, and smaller teams with less human middleware.
2. Garry Tan demonstrates the practical engineering workflow: GStack turns a coding agent into a process-driven engineering team with office hours, planning, adversarial review, design exploration, browser QA, and shipping checks.

The shared message is that AI-native company design is not just "use a coding assistant." It requires a repeatable operating process that captures context, improves plans before building, validates output, and lets multiple agents work in parallel under human direction.

## Video 1: How to Make Claude Code Your AI Engineering Team

Garry Tan presents GStack as an open-source toolkit for turning Claude Code and other coding agents into an AI engineering team. His framing is "thin harness, fat skills": keep the wrapper simple, but encode specialist workflows as reusable skills.

The demo starts with `office-hours`, a skill modeled on YC partner office hours. Instead of immediately building a tax-document app, the agent asks forcing questions about evidence, user pain, competitive alternatives, business model, and wedge strategy. The idea evolves from simple 1099 aggregation into a broader workflow involving visible browser automation and CPA handoff.

The workflow then moves through adversarial review. The agent finds issues such as missing privacy handling, missing 2FA handoff, and weak failure handling, then auto-fixes many of them before the plan is approved.

The demo also shows design exploration through multiple generated UI directions, followed by selection and iteration. Garry then describes the broader GStack sprint process:

- `office-hours` for idea pressure-testing;
- planning and CEO/engineering/design/devex review;
- design exploration;
- code generation;
- code review;
- browser QA through Playwright/Chromium tooling;
- ship checks before landing on main.

The most operational part is the parallel worktree model. Garry describes running many coding-agent sessions at once, each tied to a work item or pull request, while using process and review skills to keep the work from collapsing into unreviewed chaos.

### Analysis

The useful pattern is not just "GStack is a toolkit." It is that AI engineering needs a process stack:

- product judgment before implementation;
- adversarial review before commitment;
- design exploration before UI lock-in;
- browser-level QA before shipping;
- security and supply-chain caution before merging outside contributions.

The strongest idea is that agents become useful when they are given roles, review gates, and repeatable workflows. This is closer to managing a fast engineering team than prompting a single assistant.

### Risks / Open Questions

- Parallel agent work increases merge, review, and security load.
- Open-source PR intake plus AI-generated code raises supply-chain risk.
- "10x faster" only matters if the review gate can keep up.
- Browser automation is powerful but can become brittle without stable test targets.

## Video 2: How To Build A Company With AI From The Ground Up

Diana Hu argues that AI should not be treated as a productivity boost added to existing workflows. Her stronger framing is that AI becomes the operating system the company runs on.

The core model is the closed loop. Old companies often operate as open loops: decisions are made, work is executed, and outcomes are not always systematically fed back into the process. AI-native companies should make important processes closed loops: capture information, feed it into an intelligent system, and improve over time.

To do this, the company must become queryable. Important actions should create artifacts that agents can inspect: meeting notes, customer feedback, tickets, plans, sales calls, standups, dashboards, and engineering work. With this context, agents can evaluate previous work, identify what met customer needs, and propose better future plans.

She also describes AI software factories as an evolution of test-driven development. Humans define specs and success tests; agents generate implementation and iterate until the tests pass. In this model, humans define what to build and judge the output, while agents do much of the implementation work.

The organizational implication is a smaller company with less human middleware. If an intelligence layer can route and interpret status, decisions, and outcomes, then middle management as information routing becomes less necessary. She highlights three archetypes:

- individual contributors who build and operate directly;
- directly responsible individuals who own outcomes;
- AI-native founders who lead by using the tools themselves.

### Analysis

The useful idea is "company as closed loop." It gives a practical test for whether a process is AI-native: can the system observe what happened, compare it with goals, and improve the next iteration?

The most important boundary is that queryable does not mean surveillance by default. Public-safe implementation should focus on operational artifacts, customer-approved records, product telemetry, tickets, and decisions that belong in the business system.

### Risks / Open Questions

- Recording everything can damage trust if governance is weak.
- "No human middleware" is too broad for relationship-heavy or regulated work.
- Software factories depend heavily on good specs, tests, scenario validations, and review.
- Token usage can replace some headcount, but API spend is not automatically productive work.

## Combined Pattern

Taken together, these two videos define a useful sequence:

1. Make company work artifact-rich and queryable.
2. Use agents to turn context into better plans.
3. Run adversarial review before building.
4. Let agents implement against specs and tests.
5. Use browser QA and review gates before shipping.
6. Run many workstreams in parallel only when review capacity is real.
7. Feed failures back into tools, skills, instructions, and company process.

## Practical Takeaway

AI-native execution is not "one magical agent." It is a disciplined stack:

```text
Context -> Product judgment -> Plan -> Review -> Build -> QA -> Ship -> Learning
```

The leverage comes from making each step repeatable enough that agents can participate without hiding responsibility.
