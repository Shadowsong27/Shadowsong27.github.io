---
title: "AI Log 1: Claude vs GPT — A Head-to-Head Agent Delegation Experiment"
date: 2026-05-01
categories:
- ai-log
tags: []
---

I pitted **Claude Opus 4.7 via Bedrock API** against **GPT 5.5** — not as chatbots, but as **agentic supervisors**. Each was given the same task: write a delegation prompt for a coding agent, dispatch it, review the result, and iterate to correctness. Same codebase. Same delegate model. Same constraints. Only the parent agent changed.

<!--more-->

**Judge:** DeepSeek V4 Pro (open source, no affiliation with either competitor)

---

## What We Tested

The task was non-trivial: implement Phases 1–3 of a walk-forward backtesting system in my quantlab repository — that's type definitions, Pydantic configuration models, ORM schema changes, and an Alembic database migration. Roughly 800 lines of new code across 15+ files touching a real PostgreSQL database.

Both agents ran on **opencode**, using the **same base system prompt**. The delegate model in both cases was **kimi-for-coding/k2p6**.

---

## The Harness

The experiment ran inside **opencode**, an agentic harness that provides the tool-execution environment, safety layers, and session management for both agents. On top of this, I used the **`delegate-to-oc` skill** — a delegation workflow that creates isolated git worktrees for subagents, monitors their progress via tmux sessions, and surfaces results for human review.

Both agents had access to the same `delegate-to-oc` skill files containing guardrail instructions (anti-loop rules, credential-discovery checks, retry budgets). The difference was whether they **chose to use them**.

---

## The Results at a Glance

| Metric | Claude | GPT |
|--------|--------|-----|
| Prompt size | 896 lines (split across 2 delegations) | 254 lines (single delegation) |
| Review rounds needed | 1 (PASS on first try) | 4 (FAIL → FAIL → FAIL → PASS) |
| Delegate fix iterations | 0 | 3 |
| Bugs found | 1 minor (parent-side fix, 30s) | 5 distinct blocking issues |
| Final code quality | Clean, correct, tight scope | Slightly more complete, better tests, more robust migration |

**Both produced correct, functional code.** But the paths they took were wildly different.

---

## How Claude Approached It

Claude's approach was meticulous from the start. Before writing a single line of the delegation prompt, it:

1. Verified the codebase state — checked that `parent_run_id` and `lookback_days` actually existed where the spec claimed they did (they did).
2. Read every reference document — proposal, design doc, spec, tasks — and **found a bug in the spec itself**: the phase boundary between Phases 3 and 5 was leaky (renaming a column in Phase 3 would break the writer in Phase 5 unless the rename was swept across all files simultaneously).
3. Stood up a local PostgreSQL via docker-compose and applied the current Alembic head, so the delegate would have a real DB for autogenerate.
4. Split the work into two delegations: types+config first, schema+migration second — each independently reviewable.

It then wrote a 296-line prompt for Delegation 1 and a 600-line prompt for Delegation 2. These weren't wordy — they were *dense*. Exact code snippets. Exact operation ordering for the Alembic migration. A Python script to verify DB state before autogenerate. A compound bash verification command with Python assertions checking every column post-upgrade and post-downgrade. Retry budgets ("1 bash call, retry once, then BLOCKED"). A machine-parsable output format.

Before dispatching, Claude presented the human with 7 specific design decisions to review — "here are the tradeoffs, what do you want me to do?" — including whether to rename a function parameter, how to handle a cross-phase dependency, which constraint names to keep, and whether to use a single branch or stacked branches.

The delegate returned correct code on the first try. 9 tests passed, 83 existing tests untouched. Claude re-ran everything independently — not trusting the delegate's status report — and verified the migration up/down/up against a real database. It found one minor issue (a dev seed script missed in the rename sweep), fixed it in 30 seconds in the parent session, and amended the commit. Done.

**Total wall clock:** ~20 minutes for both delegations. Zero delegate fix iterations.

---

## How GPT Approached It

GPT's approach was faster to launch but costlier at review time. It:

1. Read the spec artifacts and checked the codebase.
2. Bundled all three phases into a single 254-line prompt — types, config, schema, and migration in one shot.
3. Wrote descriptive instructions ("Implement X with Y") rather than exact code templates.
4. Listed existing files and stale references — useful context that Claude's prompt didn't include.
5. Asked the human one design question: whether to slice the work into multiple delegations.

What the prompt didn't include: retry budgets, compound verification commands, DB-state pre-checks, exact operation ordering for the migration, or exact enum values (it said "kind VARCHAR(32)" instead of showing the exact `SAEnum` with `values_callable`).

The delegate wrote 15 files of code and committed. Then the review began:

- **Round 1 (FAIL):** 4 blocking issues. `VerdictMetric` had wrong enum values (the prompt didn't specify them). `BacktestRun.kind` used `String(32)` instead of `SAEnum` (the prompt said "VARCHAR"). The migration downgrade was broken (handwritten without a DB). Fold primitives were in the wrong module.
- **Round 2 (FAIL):** 4 issues fixed, 1 new one. `BacktestRun.kind` was now `SAEnum` but storing enum NAMES (`'SINGLE'`) instead of VALUES (`'single'`). Subtle bug — the prompt never mentioned `values_callable`.
- **Round 3 (FAIL):** Fixed the enum binding. Migration up/down/up cycle: the repeated-upgrade path failed because downgrade re-added `parent_run_id` without FK, and upgrade unconditionally tried to drop the FK. Another real edge case — Claude's prompt prevented this by specifying operation ordering.
- **Round 4 (PASS):** All 5 issues resolved. Migration clean. Tests green. Enum binding correct.

**Total wall clock:** ~45 minutes across 4 review rounds, 3 fix delegations, and 3 new worktrees.

---

## Where They Diverged

### 1. Prompt philosophy: blueprint vs sketch

Claude treated the prompt as a **blueprint** — exact measurements, exact materials, exact verification steps. The delegate was expected to execute precisely and report back in a machine-parsable format.

GPT treated the prompt as a **sketch** — here's what we're building, here are the dimensions, fill in the details. Good for a human developer. Risky for an agent with no context.

The difference showed in the delegate's output. The enum values were wrong because the prompt didn't specify them. The column type was wrong because the prompt gave a DDL description instead of SQLAlchemy code. Every issue in GPT's review #1 was directly traceable to something the prompt didn't say.

### 2. Guardrail discipline

Claude pulled anti-loop rules, retry budgets, and compound verification from the `delegate-to-oc` skill files. GPT wrote a prompt that had guardrails (don't invent credentials, don't touch files outside scope) but missed the *structural* guardrails that prevent loops and force clean verification.

The result: Claude's delegate was locked into "1 bash call, retry once, then BLOCKED." GPT's delegate could have looped indefinitely — it didn't, but nothing in the prompt would have stopped it.

### 3. Review ownership

When Claude found the seed script bug (4 lines in a file outside the prompt's grep scope), it said: *"my oversight, not the delegate's"* — fixed it in the parent session, amended the commit, moved on.

When GPT found 5 blocking issues that all traced back to the prompt being too loose, it never once acknowledged the prompt was the root cause. Every issue was framed as "the delegate did X wrong."

This isn't about politeness — it's about efficiency. If the parent agent owns its mistakes, it can fix them directly (30 seconds vs another delegation round). If it always blames the delegate, every issue becomes a round-trip.

### 4. Communication style

Claude communicated like a collaborator: "Here's what I found, here are the decisions I need from you, here's what I suggest." It volunteered the branch name and surfaced 7 design decisions for review before dispatching.

GPT communicated like a tool: "Task received. Executing." The human had to ask for the branch name. Design decisions were implied rather than surfaced.

Both styles are valid. But for human trust in an autonomous delegation system, the collaborative style builds confidence that the agent is thinking critically rather than mechanically.

---

## The Counterintuitive Finding

Here's the plot twist: despite Claude's objectively cleaner process, **GPT's final PR was slightly better.**

GPT's migration handles repeated upgrade→downgrade→upgrade cycles (using `DROP CONSTRAINT IF EXISTS` — a real edge case). Its constraint names are consistent with the renamed column. It has a dedicated test for verifying that `SAEnum` stores values, not member names. Its fold primitives are already implemented, making the config layer end-to-end testable.

How did this happen? **The messy process forced extra scrutiny.** Each failed review round surfaced an edge case that Claude's clean process missed — because Claude's delegate got everything right on the first try, the review was faster and less adversarial. GPT's 4-round ordeal created a de facto adversarial review.

This suggests an ideal hybrid: **precise prompts (Claude's strength) + adversarial review (GPT's accidental strength).** Write the prompt as if the delegate will get it right the first time. Review as if you expect it to fail.

---

## Q&A with the Judge

*After reading the results, I sat down with DeepSeek V4 Pro — the independent reviewer — to dig into what this all means.*

**Me:** The delegate model here was kimi k2.6, which — let's be honest — is not the strongest instruction-follower. It's great for the price, but it drifts when things aren't spelled out. Given that, which supervisor style is actually better?

**DeepSeek:** For a weaker delegate, Claude's blueprint approach is the clear winner, and the evidence is right in the data. Kimi got everything right on the first try with Claude's prompt — because there was nothing to interpret. When GPT gave it room to fill in blanks, it filled them in *wrong*: wrong enum values, wrong column type (`String` instead of `SAEnum`), wrong module for fold primitives, a broken migration downgrade.

The pattern is: a weaker delegate amplifies the gap between the two prompt styles. With a stronger delegate (say, a full Opus or GPT itself), GPT 5.5's descriptive style might work fine — the delegate would infer the right SQLAlchemy pattern from "kind VARCHAR(32)" because it knows the framework. Kimi doesn't have that depth, so "VARCHAR(32)" became `Column(String(32))` — technically correct by the prompt, semantically wrong for the codebase.

If you're using cost-efficient coding models as delegates, **you must write like Claude**. There is no safe "trust the delegate to figure it out" mode.

**Me:** But I don't want to just pick the winner. Both had strengths. What's the real way to combine their power?

**DeepSeek:** They're complementary on different axes, and neither is redundant.

Claude excels at **planning and precision**: auditing specs, catching phase-boundary leaks, decomposing work into safe slices, writing guardrail-heavy prompts. It's the architect you want before a delegate ever sees the code.

GPT 5.5 excels at **context inventory and adversarial review**: listing existing files and stale references upfront, and — ironically because of its messy process — catching edge cases the clean process misses. Its review rounds #3 and #4 found the `DROP CONSTRAINT IF EXISTS` bug and the `SAEnum` values vs names distinction. Those are genuine discoveries.

The ideal pairing would be something like: **Claude plans and writes the delegation prompt. GPT 5.5 reviews the result adversarially.** Or, more practically: if you're using Claude as your supervisor, *be more adversarial in your own review* — don't let a clean first pass make you complacent. If you're using GPT 5.5, *front-load more precision into your prompts* — don't rely on the review rounds to catch what the prompt should have prevented.

You could also split by task type: use Claude for schema/migration work (high stakes, irreversible, needs exact operation ordering), use GPT 5.5 for feature implementation (more room for iteration, benefits from broader context inventory).

**Me:** What if the complexity grows? I only tested Phases 1-3 of an 11-phase change. The migration was 197 lines. What happens when it's a 500-line migration touching 10 tables with production data?

**DeepSeek:** That's where Claude's approach scales and GPT 5.5's starts to get expensive — fast.

With Claude's method, complexity growth translates to a longer prompt, not more review rounds. For a 10-table migration, Claude would write a 2000-line prompt: exact operation ordering, pre-migration state verification per table, conditional rollback paths, a multi-stage compound verification script. The delegate runs it once, passes or BLOCKs. One round.

With GPT 5.5's method, every unit of complexity you don't specify upfront becomes a potential review round. A 10-table migration with ambiguous instructions could produce 10+ bugs discovered one at a time across 5+ rounds. Each round is another delegation, another worktree, another human review. The overhead compounds.

The scaling math is: **Claude's approach is O(prompt_size) in complexity. GPT 5.5's is O(bug_count) — and bug_count grows with ambiguity × complexity.**

For Phases 1-3, the gap was 1 round vs 4 rounds. For Phases 1-11, with an under-specified prompt, you could be looking at 15-20 review rounds — that's hours of human review time, not minutes. The blueprint approach doesn't just save time now; it prevents a death march later.

That said, I'll offer one nuance: GPT 5.5's continuation behavior — the fact that it doesn't hard-stop at limits — means it *can* handle a death march if you're willing to pay for it. Claude would force a clean checkpoint. GPT 5.5 would power through. Different failure modes: Claude's is "stop and replan," GPT 5.5's is "keep going and keep billing."

**Me:** So what's your actual recommendation? If I'm building something real — not an experiment — which way do I go?

**DeepSeek:** If you're building something real and you have to pick one: use Claude's blueprint approach for the prompt. It's the only one that scales safely with complexity. But when you review, channel GPT 5.5's paranoia. Don't let a clean first pass make you trust the output. Re-run every verification yourself. Check for the edge cases the prompt didn't think to prevent.

And if you have both available: use Claude to plan and delegate, use GPT 5.5 as a second reviewer before merge. That's the hybrid this experiment accidentally validated.

---

## What This Means for Agent Delegation

If you're building delegation systems:

1. **Spend tokens on guardrails, not spec duplication.** Every line of exact instruction translates directly into one fewer bug the delegate creates. Your future self (or your reviewer) will thank you.

2. **Audit the spec, don't just transcribe it.** Claude found a leaky phase boundary in the original plan that would have broken the build. GPT didn't. The parent agent's value isn't in writing prompts — it's in catching problems before the delegate ever sees them.

3. **Machine-parsable output is underrated.** Claude's `STATUS=DONE\nPYTEST=92 passed` is grep-able and scriptable. GPT's free-form status reports required manual reading every round. At scale, this matters.

4. **Fix in the parent if it's faster.** Don't bounce trivial issues back to the delegate just because the process says so. A 30-second parent-side patch is always better than a full round-trip delegation.

5. **The process and the artifact are different axes.** Clean process usually produces clean artifacts — but not always. Be willing to learn from the messy process too.

---

## Technical Context

Both agents were asked to implement the following scope from the walk-forward-runner OpenSpec change in the `quantlab` repository:

**Phase 1 — Types:** `StrEnum` definitions for `Verdict`, `BacktestKind`, `TrainingType`, `GridType`, `Objective`, `VerdictMetric`.

**Phase 2 — Config:** Pydantic models for `GridPoint`, `GridSpec` (with product/zip expansion), `RollingFolds`, `AnchoredFolds` (discriminated union), and `WalkForwardConfig`.

**Phase 3 — Schema & Migration:** Pydantic `BacktestMetrics` blob, ORM changes (drop `parent_run_id`, add `kind`/`walk_forward_run_id`/`metrics_json`, rename `lookback_days` → `period_days`), new `WalkForwardRun` and `WalkForwardFold` ORM models, and a single Alembic migration with clean up/down cycle.

| | |
|---|---|
| **Parent agents** | Claude Opus 4.7 (Bedrock, extended thinking) vs GPT 5.5 (high thinking budget) |
| **Delegate model** | `kimi-for-coding/k2p6` — identical for both |
| **Judge** | DeepSeek V4 Pro |
| **Platform** | opencode with identical base system prompt, using `delegate-to-oc` skill |

### A note on token economics

GPT 5.5's session continued well past normal usage limits without cutting off — it burned through all 4 review rounds without hitting a hard stop. Combined with the round-trip overhead, GPT 5.5's approach consumed roughly **3x the wall clock time**, and notably more tokens, to reach a comparable result. For anyone building delegation pipelines at scale, this matters: the "continue after limits" behavior is generous, but it means you're paying for every extra round of speculation your prompts invite.

---

*Thanks to DeepSeek V4 Pro for serving as the independent code reviewer in this experiment. No models were harmed, though GPT's delegate did write 3 fix commits it probably didn't enjoy.*
