---
name: evals
description: >
  Runs model exams against the eval golden sets deposited by the workflow skill: "evals.run
  <seat> [candidate model]" exams a candidate on one seat's cases (seats: spec, plan, reviewer,
  mechanical), grades with the incumbent strict reviewer plus a human spot check, scores quality
  AND cost/latency, and writes a one-page scorecard to evals/scorecards/. "evals.list" shows set
  and scorecard status. Trigger only on explicit "evals.run ...", "/evals.run ...", or
  "evals.list" invocations -- never on casual mentions of evals or testing. Routing changes are
  propose-only: this skill recommends fallback-order edits to the workflow skill's tables but
  never applies them. Expensive by design; always confirms case count and cost appetite before
  running.
---

# evals

Exams candidate models against the golden sets that `workflow` deposits at wrap
(`evals/<seat>/`). The triad: **workflow deposits → checkup detects → evals.run exams.** A model
earns a seat in the `workflow` routing tables only by winning here — no seat without a job, no
job without a score.

Run when a new model releases, when checkup flags an unexamined model, or quarterly. Never
mid-workflow-run, and never on someone else's initiative — the human invokes it.

## Commands

- `evals.run <seat> [<harness> <model> <effort>]` — exam one seat. If the candidate isn't given,
  ask. Seats: `spec`, `plan`, `reviewer`, `mechanical`.
- `evals.list` — read-only: cases per seat, malformed/stale cases, existing scorecards, and which
  routing-table models have no scorecard. (Overlaps `checkup evals` on purpose — this is the
  quick pre-run view.)

## Procedure — `evals.run`

**1. Preflight (before any model call).** Load `evals/<seat>/*.md`; verify each case is
self-contained (input + approved output + grading notes + provenance — skip and flag any that
aren't; don't fail the run on one bad case). State the bill upfront: N cases × (1 candidate call
+ 1 grading call) plus the candidate's effort level, and **confirm cost appetite with the human
before proceeding**. Confirm the candidate is actually available in its harness (`/model`).

**2. Run the candidate — one case at a time, fresh context each.** For every case, present the
case *input* to the candidate in a fresh session/context with the same working conditions the
seat gets from the `workflow` skill (the seat's phase instructions, same effort, read access to
the repo state the case assumes — but never the approved output). Capture the candidate's full
output verbatim, plus tokens/latency where the harness reports them. No retries beyond one
mechanical failure (timeout/refusal) per case; a second failure scores the case as failed.

**3. Grade with the incumbent strict reviewer.** The grader is the current Phase 4 reviewer
seat from the `workflow` routing tables — and **cross-vendor where possible**: if the candidate
is from the grader's own family, prefer a different-vendor grader of comparable strength and
note the substitution on the scorecard. Per case, the grader compares candidate output against
the approved output using the case's grading notes and returns pass / partial / fail with a
one-line reason. Reviewer-seat cases are strict: the candidate must catch **every** finding the
case plants; a missed P0 is an automatic fail regardless of what else it caught.

**4. Human spot check.** Present all failures plus a ~20% sample of passes for the human to
confirm or overturn. Grader verdicts are hypotheses, not truth.

**5. Score quality AND cost/latency.** Totals per seat: pass rate (weighted — reviewer P0
catches matter more than style notes), cost per case, latency per case. A candidate that ties
on quality but halves the cost is a win; say so explicitly.

**6. Scorecard — one page, always.** Write `evals/scorecards/<YYYY-MM-DD>-<seat>-<model>.md`:
candidate (harness/model/effort), grader used, per-case table (case → verdict → reason), totals,
cost/latency, spot-check overturns, and a **recommendation**: promote to the seat, keep as
fallback, or reject — with the one paragraph of reasoning a future reader needs. Commit it.

**7. Routing is propose-only.** If the recommendation is promote/demote, print the exact edit to
the `workflow` skill's routing/effort tables (which rows, old → new) — and stop. The human
applies it. This skill never edits the `workflow` skill, and never touches product code at all.

## Ground rules

- **Writes only `evals/scorecards/`** (and flags, in its report, bad cases for the human to fix).
  Never edits cases, never edits the workflow skill, never edits code.
- **Bounded.** One seat per invocation; if the human asks for "all seats", run them as separate
  sequential exams with a cost confirmation each.
- **Honest failure.** If a set is too small or too weak to discriminate (< ~5 usable cases, or
  every case trivially passed by everything), say the exam is not meaningful yet and recommend
  depositing better cases via the workflow's `[durable→eval]` tag instead of publishing a
  hollow scorecard.
- **Provenance discipline.** The scorecard names exact model IDs and effort levels, not
  families — "Sonnet 5 xhigh via Copilot CLI", not "Claude".

## Relationship to the other skills

- `workflow` — deposits golden cases at wrap (`[durable→eval]` → `evals/<seat>/`); owns the
  routing tables this skill's recommendations target.
- `checkup` — audits set health and flags unexamined models (`checkup evals`); this skill is
  what it delegates the actual exam to.
