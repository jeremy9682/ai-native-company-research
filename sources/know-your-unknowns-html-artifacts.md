# Know Your Unknowns — HTML Artifacts for LLM-Assisted Development

Source: https://thariqs.github.io/html-effectiveness/unknowns/
Parent collection: https://thariqs.github.io/html-effectiveness/
Publisher: thariqs.github.io (personal)
Speaker / Author: Thariq Shihipar (Anthropic)
Published: 2026 (Claude Fable 5 era)
Accessed: 2026-07-05

## Summary

The source is a collection of eleven self-contained HTML artifacts, each demonstrating a way to surface *unknowns* when working with a coding agent. Its framing is "the map is not the territory": the **map** is everything you hand the model before it works (prompt, spec, reference, mock); the **territory** is the real system — the codebase, its history, its undocumented constraints, the actual intent. The gap between them is your **unknowns**, especially the *unknown-unknowns* you don't know to ask about.

Each artifact turns a slice of that gap into an **inspectable decision surface** produced *before* irreversible work begins. A companion claim runs through the collection: for anything with spatial structure (diffs, design systems, decision tables, side-by-side variants), an HTML page beats linear Markdown — it trades a document you'd skim for one you'd actually read and interact with.

The eleven artifacts are organized by when they help: eight before implementation, one during, two after.

## Key Ideas

Paraphrased patterns (not the source's exact prompts):

**Before implementation**
- **Blindspot pass** — have the model enumerate *your* unknown-unknowns in an unfamiliar module as a typed, evidence-linked list (landmines, unwritten conventions, missing concepts, prior reverted attempts), then fold every finding into a sharper, constraint-numbered instruction.
- **Teach me my unknowns** — when the gap is *vocabulary*, not scope, generate a short interactive lesson (mental model + vocabulary ladder + one hands-on control) so the requester can then specify precisely.
- **Divergent design directions** — render the same content in several deliberately different visual languages so a non-designer can react and cherry-pick, instead of specifying taste up front.
- **Mock before you wire** — a throwaway fake-data mock with a few A/B decision chips on the genuinely ambiguous forks, so layout/scope questions are settled before touching real code.
- **Brainstorm a ranked menu** — turn a vague problem into ~10 evidence-grounded options ordered cheapest→most-ambitious, and let the human filter by what resonates.
- **The blast-radius interview** — one question at a time, ordered by how much the answer changes the architecture (architecture > data > permissions > UX > polish), each with a few opinionated options and an escape hatch, ending in a decisions table.
- **Point at a reference (semantics map)** — before porting an existing reference, emit a structured restatement (prose + aligned pairs + a preserved/changed/dropped table + edge cases) gated behind an explicit "semantics confirmed" sign-off.
- **The tweakable plan** — sort a plan by *likelihood-of-being-changed* rather than execution order; put the high-tweak decisions up top as toggles, self-flag the weakest link, and collapse the trusted mechanical work.

**During implementation**
- **Deviation log** — keep an append-only note as the build proceeds, separating plan-confirmed steps, incidental discoveries, forced deviations (with the conservative call made), and items that need a human decision.

**After implementation**
- **Buy-in doc** — a lead-with-the-demo packet for asynchronous sign-off: working demo, then the decisions, then a named approval matrix.
- **Comprehension gate** — a walkthrough of a change followed by a short scenario check the reader must pass before an irreversible step, because a green CI run is not proof of understanding.

## Analysis

What we think is most valuable here:

- The unifying move is "**produce a decision surface before irreversible work**." Most mature methodologies (Shape Up, RFC/design-doc culture, Amazon's PR/FAQ, TDD) *point at* this idea but leave the surface implicit or unstructured. These artifacts make it concrete and reactive.
- The single highest-leverage, least-common technique is the **during-build deviation log**. Almost no mainstream workflow captures the silent drift between a ratified plan and what the agent actually did; this closes that loop cheaply and feeds lessons back into the next iteration.
- The **blast-radius interview** and the **tweakable plan** are cheap reframes of things teams already do (requirements gathering, implementation plans) that sharply reduce rework, because they force the consequential decisions to the front.
- Mapped onto a normal pipeline: techniques 1–8 harden the *plan* stage, the deviation log instruments *implement*, and the buy-in doc + comprehension gate govern *review/ship*.

## Practical Applications

Framed as proposals, not claims the source endorses:

1. **Fold selected techniques into a team dev workflow.** A pragmatic subset — a blindspot pass and a blast-radius interview gated to only fire on high-risk ("blast radius") changes, an always-on deviation log in the implement step, and a scenario-confirmation gate at review — adds a discovery layer and a during-build audit trail without adding ceremony to routine edits.
2. **A customer-facing requirements loop.** The same techniques (interview → semantics map → one mock → decision table → sign-off) can drive a throwaway, read-only "requirement studio" that confirms scope with a non-technical stakeholder *before* anyone builds the real thing — provided the generated surface renders **declarative data through fixed, vetted components** and never executes model-written code, stays read-only/dry-run, and produces a durable, versioned "requirement contract" that feeds the real build. For a business (non-engineer) audience, replace the "quiz" with a *scenario-confirmation checklist* ("let me restate what happens; correct me") — quizzing a customer is patronizing.
3. **Pair with a Codex-in-Claude-Code setup.** OpenAI's [`codex-plugin-cc`](https://github.com/openai/codex-plugin-cc) plugin adds durable background-job control (`/codex:review`, `/codex:rescue`, `/codex:status`, resume, an optional stop-time review gate) that hand-rolled `codex exec` orchestration otherwise reimplements in prose. Use the plugin for single jobs you'll want to check on / resume / gate; keep raw CLI for multi-agent fan-out it can't express.
4. **Decide who plans vs. executes between Claude and Codex.** Two mirror-image splits exist: *Claude plans, Codex executes* vs. *Claude executes at scale, Codex directs + reviews*. A workable default: let the fast, web-capable, parallel model execute and **always end with the slower, higher-ceiling model reviewing the final diff**; flip to reasoning-model-leads-implementation for money/auth/migration/schema/PII/irreversible-write tasks, or when failure would be hard to detect after ship. Never make the model with weak web access the web-facing executor.

## Risks / Open Questions

- **Over-generation.** Producing many demos/variants per decision burns time and tokens; usually one concrete mock plus explicit exclusions beats a dozen variants.
- **Demo drift.** A mock that diverges from real system behavior gets a stakeholder to sign off on a fantasy; keep generated surfaces grounded in real fields/behavior and mark synthetic data clearly.
- **Ceremony creep.** Gating everything (interviews, quizzes, sign-offs) on trivial changes adds burden without payoff; trigger the heavier techniques only on genuinely high-blast-radius work.
- **Security of generated UI.** Executing model-generated HTML/JS inside a privileged app is an injection/permission hazard; prefer declarative specs rendered by fixed components.
- **Open question:** which of the eleven generalize cleanly to non-code domains (ops, design, writing) vs. which are code-specific.

## Related sources
- Nathan Onn — *Plan with GPT-5, execute with Claude Code*: https://www.nathanonn.com/the-codex-claude-code-workflow-how-i-plan-with-gpt-5-and-execute-with-claude-code/
- *"Claude Overreaches, Codex Underreaches"*: https://medium.com/@lakshminp/claude-overreaches-codex-underreaches-im-still-figuring-out-how-to-use-both-f98b124691f5
- DataCamp — *Codex vs Claude Code*: https://www.datacamp.com/blog/codex-vs-claude-code
- Builder.io — *Codex vs Claude Code*: https://www.builder.io/blog/codex-vs-claude-code
- Claude Code docs — *Orchestrate subagents at scale*: https://code.claude.com/docs/en/workflows
