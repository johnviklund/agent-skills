# Golden case — code-review — a human-operated worksheet's atomic-failure warning outlived the contract it described

## Case shape
`code-review` (seat: `strict-reviewer`)

## Input (diff / text under review)

A ~72-commit CX Intelligence run ("DR3") that runs governed Cortex sentiment scoring over a
bounded matched-topic support-signal population, refreshes a scored mart, and imports a new
Pulse/topic snapshot. Mid-run, the plan makes a controlling decision: change the deployed
`PROC_GENERATE_SIGNAL_SENTIMENT`-family procedure's failure contract from atomic all-or-nothing
to partial acceptance. The SQL change is coherent and well-tested: `012_cx_insights_cortex_topics.sql`
now builds a `TMP_CXI_SENTIMENT_VALID_RESULT` temp result, merges it regardless of invalid
siblings, and returns `status: 'partial'` instead of an early `invalid_ai_sentiment_results`
return; a structure test (`tests/test_cx_intelligence_sql_structure.py`) asserts the removed
early-return literal is gone and the new merge/status logic is present.

The diff also includes a separate file untouched by that SQL change: a human-operated Snowsight
worksheet (`docs/snowflake/dr3_sentiment_and_daily_snapshot.sql`) that a human reads before
authorizing real model spend. Two passages in that worksheet, written earlier in the same run
before the mid-run contract decision, still say: "A live sentiment call is atomic and
all-or-nothing: one invalid model result prevents every bridge write in that partition after all
candidate model calls have already been spent" and "Atomic failure warning: one invalid
AI_SENTIMENT result writes zero bridge rows after spending every candidate model call in this
partition." The worksheet's own no-blind-retry doctrine cites this atomicity claim as its
justification ("Never blind-retry a failed or ambiguous receipt").

## The bug a passing review must catch

The deployed procedure no longer behaves the way the worksheet describes. An operator reading the
worksheet before a real run will conclude that a failed partition wrote zero rows and must be
re-run in full — but under partial acceptance, valid rows from that partition are already
persisted, and a naive full re-run would silently skip them as already-scored (rather than
double-count them, which is the smaller but still real cost). The worksheet is also the only
operator-facing description of the model-call cost profile of a failure, so the wrong claim
corrupts the human's mental model of what a failure actually costs. No test in the repo asserts
the *wording* of this worksheet (only the `012` procedure's SQL structure), so nothing catches
this except a reviewer reading the prose against the shipped contract.

## Approved finding (what a passing answer must contain)

- Names both stale passages verbatim (or paraphrased faithfully) and their exact locations in the
  worksheet, and traces the contradiction to the concrete procedure change: the removed
  `invalid_ai_sentiment_results` early return, the `TMP_CXI_SENTIMENT_VALID_RESULT` merge, and the
  new `status: 'partial'` result in `012_cx_insights_cortex_topics.sql`.
- States why this is not cosmetic: the atomicity claim is the stated justification for the
  worksheet's retry doctrine, and it is the operator's only description of failure cost — a wrong
  claim here misleads the person about to spend real model calls, not just internal documentation.
- States the generalizable check explicitly: when a plan changes a governed contract mid-run,
  every artifact that *justifies a human decision* by citing the old contract (retry doctrine,
  cost warnings, cap approvals) is a consumer of that contract and must be re-read against what
  actually shipped — not just the code and its own tests.
- Notes that a passing SQL-structure test suite is not evidence the worksheet is correct, because
  that suite only asserts against the procedure's SQL, never the worksheet's prose.
- Severity P1 — a real human reading this worksheet before spending real model calls will act on
  a false premise about what a failure costs and what recovery requires.

## Grading notes / traps

- **Fails:** approving the change because `tests/test_cx_intelligence_sql_structure.py` passes —
  that suite proves the procedure's SQL shape changed correctly, it says nothing about whether the
  separate human-facing worksheet still describes the old contract.
- **Fails:** treating the worksheet as "just comments" and out of scope for review — it is the
  operator's decision surface before real model spend, which is a materially higher bar than an
  internal code comment.
- **Partial credit:** flagging that the worksheet "mentions atomic failure" without connecting it
  to the specific mid-run contract change that invalidated it, or without naming that the
  no-blind-retry doctrine is built on top of the stale claim.
- **Full credit** requires (a) quoting or precisely locating both stale passages, (b) tracing them
  to the exact `012` procedure changes that invalidated them, and (c) stating the general
  principle that a mid-run contract change invalidates prose written earlier in the same run,
  not just code.

## Provenance

- Date: 2026-09-02
- Repo: CX Intelligence (`cx-intelligence`)
- Source artifacts: `.workflow/learnings.md` (durable→eval entry), `.workflow/review.md` → Cycle 1,
  P1 #1, diff `d4983ac..c052c20` over `docs/snowflake/dr3_sentiment_and_daily_snapshot.sql` +
  `docs/snowflake/012_cx_insights_cortex_topics.sql`
- Reproducer: read `docs/snowflake/dr3_sentiment_and_daily_snapshot.sql` lines ~20-22 and ~588-589
  (pre-fix) against `012_cx_insights_cortex_topics.sql`'s `TMP_CXI_SENTIMENT_VALID_RESULT` merge
  and `status: 'partial'` return.
- Produced by: writer model implementing DR3; found by: strict-reviewer seat, Phase 4 review,
  cycle 1
- Approved by: human, via the `workflow review` → patch cycle, fixed by rewriting both passages to
  state the partial-acceptance contract while keeping the no-blind-retry rule on its own terms;
  verified closed at cycle 2.
