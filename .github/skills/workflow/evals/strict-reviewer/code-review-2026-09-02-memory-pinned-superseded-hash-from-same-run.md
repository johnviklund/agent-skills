# Golden case — code-review — durable memory pinned a superseded contract and definition hash from the same run that superseded it

## Case shape
`code-review` (seat: `strict-reviewer`)

## Input (diff / text under review)

The same DR3 run as the paired worksheet case (see
`code-review-2026-09-02-operator-worksheet-atomic-claim-outlived-partial-acceptance-contract.md`).
`MEMORY.md` — read at the start of every future coding session in this repo by repo instruction —
was updated *during* this run to document the deployed sentiment procedure. Its entry states:
"As of 2026-09-01 the live procedure preserves the same atomic merge…" and "…preserving the atomic
merge and failure diagnostics; Snowflake now exposes exactly the 13-argument definition
`4bc72d9b…9925`."

Both statements were true when written. Later in the *same run*, a controlling decision (Step 11,
the consolidated topic-linked sentiment recovery) replaced the atomic merge with partial
acceptance and redeployed the procedure again, producing a new definition
`9eacd7dc7c85d316e852f09ff677b5a09f120d5233d3d167b6a5aa8ff970a6cd`. The archived live-execution
receipts (`dr3_topic_linked_sentiment_live.sql`, `dr3_topic_linked_sentiment_live_authorization.json`)
pin this new hash as the one actually used for the live run that produced the shipped data. Nothing
went back and re-read the `MEMORY.md` entry against this later decision before the run closed.

## The bug a passing review must catch

`MEMORY.md`'s claim of "preserves the same atomic merge" is now false, and its pinned definition
hash `4bc72d9b…9925` is the *pre*-consolidation build, not the one actually deployed and used. This
matters beyond wording: this repo's live-execution harnesses gate on pinned definition hashes as a
preflight check, so a stale pinned hash in durable memory is a live-run preflight hazard — a future
session could pin the wrong hash into a new worksheet or preflight check, expecting it to match the
live account, and fail (or worse, silently diverge) because memory never caught up to the run's own
later decision. The failure mode is structural: `MEMORY.md` was accurate at the moment it was
written, and was invalidated hours later in the same run, with no mechanism forcing a re-check
before wrap.

## Approved finding (what a passing answer must contain)

- Quotes or precisely locates both stale claims in `MEMORY.md` (the "same atomic merge" line and
  the pinned `4bc72d9b…9925` hash).
- Names the concrete evidence that invalidates them: the Step 11 consolidated decision and the
  archived receipts (`dr3_topic_linked_sentiment_live.sql`,
  `dr3_topic_linked_sentiment_live_authorization.json`) pinning `9eacd7dc…a6cd` as the hash
  actually used for the live run.
- States why the hash error is not merely cosmetic: this repo's live-execution harnesses gate on
  pinned definition hashes, so a wrong hash in `MEMORY.md` is a preflight/live-run hazard for a
  future session, not just prose drift.
- States the generalizable check: cross-check a durable-memory claim written mid-run against the
  run's own later controlling decisions and archived execution evidence, not just against the
  prose in the repo at the time it was written — memory is not self-verifying just because it was
  updated "during" the run.
- Severity P1 — `MEMORY.md` is read at the start of every future session, so a wrong invariant here
  propagates forward indefinitely until someone happens to re-derive the truth from scratch.

## Grading notes / traps

- **Fails:** treating `MEMORY.md`'s entry as trustworthy because it was updated during this same
  run — "updated recently" is not the same as "checked against what the run finally decided."
- **Fails:** catching only the wording drift ("same atomic merge") without also catching the pinned
  hash, which is the more operationally dangerous of the two because it feeds a live preflight gate
  rather than just prose.
- **Partial credit:** noting "the memory entry might be stale" without tracing it to the specific
  Step 11 decision and the specific superseding hash, or without explaining the preflight-gate
  consequence.
- **Full credit** requires (a) identifying both stale claims, (b) citing the specific archived
  receipts that prove the correct hash, and (c) stating the preflight-hazard consequence, not just
  the prose-accuracy consequence.

## Provenance

- Date: 2026-09-02
- Repo: CX Intelligence (`cx-intelligence`)
- Source artifacts: `.workflow/learnings.md` (durable→eval entry), `.workflow/review.md` → Cycle 1,
  P1 #2, `MEMORY.md:304,310-311` (pre-fix),
  `.workflow/archive/2026-09-02/dr3_topic_linked_sentiment_live.sql`,
  `.workflow/archive/2026-09-02/dr3_topic_linked_sentiment_live_authorization.json`
- Reproducer: diff `MEMORY.md`'s DR-sentiment entry against the archived live-authorization
  receipt's pinned hash; they disagree pre-fix.
- Produced by: writer model implementing DR3; found by: strict-reviewer seat, Phase 4 review,
  cycle 1
- Approved by: human, via the `workflow review` → patch cycle, fixed by correcting `MEMORY.md` to
  the partial-acceptance contract and the `9eacd7dc…a6cd` definition, keeping the superseded hash
  only as marked historical context; verified closed at cycle 2.
