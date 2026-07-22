# How Anthropic Runs Large-Scale Code Migrations with Claude Code

Source:
- https://claude.com/blog/ai-code-migration
- First-hand account of the Bun port: https://bun.com/blog/bun-in-rust
- Starter kit: https://github.com/anthropics/code-migration-kit-with-claude-code

Publisher: Anthropic
Published: 2026-07-16
Accessed: 2026-07-22

## Summary

The article documents a repeatable process for using AI agents to port large
codebases between languages, built on two production cases:

- **Bun, Zig to Rust** (Jarred Sumner): roughly one million lines across 1,448
  files, ported in under two weeks. 100% of the existing test suite passed
  before merge; 19 post-merge regressions were all fixed. Measured outcomes:
  89% memory reduction in a 2,000-build benchmark (6,745MB to 609MB), 19%
  smaller binary, 2–5% speed gains on real workloads. About 4% of the code
  needed `unsafe` blocks at C/C++ boundaries. Reported cost: 5.9B uncached
  input tokens and 690M output tokens, around $165,000 in API spend.
- **Internal Python to TypeScript** (Mike Krieger's team): 165,000 lines over
  a weekend, using hundreds of agents, eight phase gates, and three
  adversarial review rounds. Compile time went from 30 minutes to about two
  seconds, binary startup became 6x faster, and a separate deployment
  infrastructure was retired.

The organizing principle is that you do not fix translated code by hand — you
fix the process loop that produced it, then regenerate. The closing rule is
"Review loop results, not code."

Four structural conditions make migration unusually AI-friendly: the work
parallelizes into thousands of independent units; the legacy code is itself
the authoritative specification; existing test suites provide objective
ground truth; and compiler errors plus test failures write the next work
queue automatically.

The process:

- **Prerequisite — establish a judge.** Classify existing tests into
  external-behavior vs. internals-dependent, rewrite portable ones as
  assertions that run against both codebases, and validate the judge itself:
  it must pass the original code and fail deliberately broken
  implementations.
- **Step 1 — rulebook, dependency map, gap inventory.** The rulebook is a
  lookup table of type/idiom translations for structure-preserving
  migrations, or a full design document for redesign migrations. Agents run
  deterministic scripts to build the dependency map. The gap inventory turns
  implicit knowledge (memory models, typing discipline) into explicit rules.
- **Step 2 — stress-test the rules, then discard the output.** One agent
  translates a few files by the rulebook, another translates the same files
  as a "senior engineer," a third derives new rules from the diffs. This
  caught two issues that would have affected all 1,448 Bun files. This step
  only applies to structure-preserving migrations; redesign migrations use
  adversarial review of the design document plus disposable end-to-end runs.
- **Step 3 — translate everything.** An implement/review/fix loop: cheaper
  models do high-volume implementation (12 parallel Claude Sonnet subagents
  in the internal case) while larger models review and maintain rules. The
  queue is rebuilt from disk state ("done" means the output file exists), so
  the migration is resumable by construction. Uncertain work is flagged with
  `TODO(port)` comments. Each translation gets two independent adversarial
  reviews with a third agent as tiebreaker. Repeated failure patterns trigger
  a rulebook fix and batch regeneration, never hand-patching. Fast compilers
  run inside the loop; slow compilers run as a separate later phase.
- **Steps 4–6 — compile, smoke-test, match behavior.** The same loop shape
  with progressively less human judgment: fixer agents burn down compiler
  error lists; crashes are grouped by root cause and fixed at the root; the
  test suite is sharded against the port, and a single build daemon is the
  only process allowed to rebuild, batching patches to serialize the most
  expensive operation. Where tests are missing, Claude designs its own
  end-to-end suite and runs it autonomously overnight (four nights in a row
  in the internal case).

Validated practices: plan specifically rather than following the guide
blindly; attend to failure patterns, not individual failures; combine
adversarial review with mechanical verification; assign models by role;
front-load human effort into the rulebook and stress test; keep queues
mechanical and resumable.

## Analysis

- **The economics claim is the headline.** A million-line port for roughly
  $165k in tokens plus two weeks of one expert's attention re-prices a
  decision that used to cost years and millions. The article's threshold — a
  year of memory-bug patches or one chronic bottleneck is now sufficient
  justification — is a genuine shift in when rewrites clear the bar.
- **The judge is the ceiling.** Every downstream "mechanical verification"
  inherits the coverage of the parity judge. Bun had a fully runnable test
  suite as ground truth; teams without one must build the judge first, and
  that is where the real human cost sits.
- **Expertise is front-loaded, not eliminated.** The eight reviewer
  categories came from Sumner's own intuition about failure modes; rulebook
  quality determines the quality of every generated file. This is leverage on
  human judgment, not its removal.
- **The most skippable-looking distinction is the most important one:**
  structure-preserving vs. redesign migration. It decides whether Step 1
  produces a lookup table or a design document, and whether Step 2 validates
  by diffing translations or by attacking the design doc. Misclassifying a
  redesign as a translation is a methodology-level failure, not a bug.
- This maps cleanly onto the repo's existing thesis: the compounding asset is
  the operating surface around the agents — judge, rulebook, queue scripts,
  review gates — not any individual agent run.

## Applications

Framed as proposals, not claims the source endorses.

**Worked example: applying the playbook to a Frappe/ERPNext-to-Go rewrite.**
ERPNext (an open-source Python ERP on the Frappe framework) is a useful
stress case because it is not one migration but three, and each layer lands
differently in the article's taxonomy:

1. **Frappe framework core** — metadata-driven ORM (DocTypes constructed at
   runtime from JSON), string-dispatched hooks, decorator-registered APIs,
   permission engine. Python dynamism is the architecture itself, so this is
   a *redesign* migration: design document plus adversarial review, not a
   lookup table.
2. **ERPNext business logic** — procedural validate/on-submit rules in
   DocType controllers. Mostly *structure-preserving*, but it calls the
   framework layer, so its translation waits on frozen interfaces or shims.
3. **Custom app layer** — smallest, best understood by its owners, with
   self-written tests: the right place to start.

Proposed adaptations:

- **Judge:** ERP systems have a natural seam — all external behavior passes
  through HTTP APIs plus database side effects. A dual-run parity harness can
  replay identical request sequences against old and new stacks and diff
  responses, database change sets, and **accounting ledger entries**. The
  ledger is mechanically checkable ground truth, cleaner than most domains
  get. Add a handful of end-to-end scenario assertions (purchase-to-ledger,
  stock transfer, return reversal, permission boundaries, pricing, report
  totals, concurrent submission), and validate the judge against the original
  stack and against deliberately broken variants.
- **Gap inventory items specific to ERP:** decimal/currency rounding policy
  (float semantics differ), timezone conventions for business dates vs.
  instants, implicit transaction semantics and on-commit hooks vs. explicit
  transactions, dynamic dispatch that must become code generation or
  registries, and permission scoping that must be reproduced mechanically
  rather than as prose rules.
- **Language choice via the article's own decision point:** compiler
  placement. Go's second-scale compiles fit inside the implement/review/fix
  loop (like TypeScript did); Rust would force a separate compile phase (like
  Bun). An ERP is IO-bound CRUD where the bottleneck is the database, not
  memory ceilings — so Go for the port, with Rust reserved for measured hot
  spots.
- **Scope as a wedge, not a bet:** run the full six-step process on the
  custom layer plus one complete business vertical (sale, stock, ledger)
  with only the minimal framework subset it needs. The deliverables are the
  reusable assets — judge harness, two-track rulebook, queue scripts, and
  real cost-per-thousand-lines data — which then price the decision on the
  remaining layers. If the wedge fails, the loss is bounded and the assets
  remain.
- **Safety posture:** all dual-run comparisons belong on shadow or sanitized
  database copies, never on a production write path.

## Resources

- Starter kit prompts: [dependency map](https://github.com/anthropics/code-migration-kit-with-claude-code/blob/main/prompts/01-dependency-map.md),
  [gap inventory](https://github.com/anthropics/code-migration-kit-with-claude-code/blob/main/prompts/02-gap-inventory.md),
  [stress test](https://github.com/anthropics/code-migration-kit-with-claude-code/blob/main/prompts/03-stress-test.md),
  [translation kickoff](https://github.com/anthropics/code-migration-kit-with-claude-code/blob/main/prompts/04-translation-kickoff.md)
- [Rulebook template](https://github.com/anthropics/code-migration-kit-with-claude-code/blob/main/templates/RULEBOOK.md)
  and [build daemon script](https://github.com/anthropics/code-migration-kit-with-claude-code/blob/main/scripts/build_daemon.sh)
- [Code modernization plugin](https://github.com/anthropics/claude-plugins-official/tree/main/plugins/code-modernization)
