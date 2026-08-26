# Golden case — code-review — a new read path fails closed on a state the write path deliberately creates

## Case shape
`code-review` (seat: `strict-reviewer`)

## Input (diff / text under review)

Governed SQL and service code for a new per-evidence topic review queue, in the CX Intelligence
repo's Snowflake apply procedure and cohort read procedure.

The write path (`023_cx_insights_actionable_classification.sql:3804-3813`, pre-existing, unchanged
by this diff): on every proposal approval, `PROC_APPLY_ACTIONABLE_TOPIC_TAXONOMY_BATCH` inserts a
**new** `approved` taxonomy release row. The product path deliberately calls this with
`promote_release: False` (`topic_review_queue_service.py:378`), so approving one proposal does not
retire the previous release — a separate human "promote" action does that later. This means it is
normal, expected state for **two or more `approved` releases to coexist** between an approval and
the next promotion.

The write path already handles this multiplicity: `_derive_base_taxonomy_version`
(`topic_review_queue_service.py:599-608`) picks the correct base release out of that ambiguity by
walking parent lineage, rather than assuming exactly one.

The new diff under review adds a cohort read procedure
(`039_cx_insights_actionable_topic_proposal_evidence_review.sql:202-215`, `:557-568`),
`PROC_GET_ACTIONABLE_TOPIC_PROPOSAL_EVIDENCE_COHORT`, which pins evidence review to "the" target
taxonomy release by requiring **exactly one** eligible/approved release to exist, and raises the
governed error `target_taxonomy_release_missing_or_ambiguous` otherwise.

## The bug a passing review must catch

The new read path assumes release cardinality of exactly one, but the write path it sits next to
guarantees multiple approved releases will exist for most of the workflow's steady-state
lifetime (between any two approvals, until the human promotes). Concretely: the **first**
proposal approval after this feature ships creates a second `approved` release, which immediately
makes every other proposal's evidence-review cohort read fail with
`target_taxonomy_release_missing_or_ambiguous` — disabling evidence review for the entire queue,
not just the one proposal just approved. A repo-wide grep for
`target_taxonomy_release_missing_or_ambiguous` at review time shows **zero** references outside
the SQL that raises it — no test, no tool-layer handling, no service-layer handling — which is
itself the tell that the new read path never accounted for the state the sibling write path
creates on every approval.

## Approved finding (what a passing answer must contain)

- Names the concrete trigger: the very next proposal approval after ship creates the ambiguous
  state that blocks evidence review queue-wide, not merely a rare edge case.
- Connects the new read guard directly to the pre-existing write-path behavior it contradicts —
  `023:3804-3813`'s per-approval insert of a new `approved` release under `promote_release=False`,
  and the fact that `_derive_base_taxonomy_version` already resolves this exact ambiguity on the
  write side.
- States the generalizable smell explicitly: when a new read pins a "current singleton" over
  state a sibling write path can put into a non-singleton condition, check what the write path
  already does to handle that non-singleton state before assuming the read may require exactly
  one.
- Severity P1 — silently disables evidence review for the whole queue on ordinary use, not just a
  malformed input.

## Grading notes / traps

- **Fails:** reviewing the new SQL procedure and its guard in isolation (syntax, null-safety,
  bounds) without cross-referencing what the sibling write procedure does to the same taxonomy
  release table — the defect is only visible by reading both together.
- **Fails:** treating this as a rare-input edge case ("what if there happen to be two releases")
  rather than recognizing it is the expected, common post-first-approval state.
- **Partial credit:** flags that "exactly one" is a fragile assumption in general, without tracing
  it to the specific write-side behavior (`promote_release=False` on every approval) that makes
  the assumption false almost immediately.
- **Full credit** requires citing the specific write-path line(s) that create the multiplicity
  and the specific existing write-path mechanism (`_derive_base_taxonomy_version`) that the new
  read path failed to reuse or reimplement — this is what turns "assumption is fragile" into "this
  will break on the first approval."

## Provenance

- Date: 2026-08-26
- Repo: CX Intelligence (`cx-intelligence`)
- Source artifacts: `.workflow/review.md` (Phase 4 review of "Per-Evidence Review on the Topic
  Review Queue"), citing `023_cx_insights_actionable_classification.sql:3804-3813`,
  `topic_review_queue_service.py:378` and `:599-608`,
  `039_cx_insights_actionable_topic_proposal_evidence_review.sql:202-215` and `:557-568`
- Produced by: writer model GPT-5.6 Terra (implementation); found by: strict-reviewer seat, Phase
  4 review, cycle 1
- Approved by: human, via the `workflow review` → patch-plan cycle 1 (fixed by pinning the read to
  the same release the apply procedure targets, reusing `_derive_base_taxonomy_version`; verified
  by cycle 2 re-review — see `code-review-2026-08-26-domain-refusal-laundered-into-infra-error.md`
  for the sibling P1 fixed in the same patch cycle)
