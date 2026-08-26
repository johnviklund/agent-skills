# Golden case — code-review — a governed domain refusal laundered into an infrastructure error by a broad `except`

## Case shape
`code-review` (seat: `strict-reviewer`)

## Input (diff / text under review)

A new governed-tool call path in the CX Intelligence repo's FastAPI backend, added to support a
per-evidence topic review queue read.

`tools.py:1119-1131` (new in this diff): when the governed Snowflake procedure returns a payload
with `status: 'failed'`, this code raises a bare `ValueError(<message>)` — no typed exception, no
error code preserved on the exception itself.

`topic_review_queue_service.py:155-157` (new call site added by this diff): wraps the new tool call
in a broad `except (ConnectionError, TimeoutError, ValueError) as exc:` and re-raises everything
caught as `TopicReviewQueueUnavailableError`.

`app.py:237-248` (existing, unchanged): maps `TopicReviewQueueUnavailableError` to an HTTP
**503**, with a message telling the operator to "check Snowflake configuration and reviewer
access."

Elsewhere in the same file, the *correct* pattern for this exact situation already exists:
`_reject_failed_governed_receipt` (`topic_review_queue_service.py:53-74`) inspects the governed
payload's `status`/error code and raises a distinct, typed exception that `app.py` maps to
**409** — the "the domain said no, ask a human" status — instead of 503. The new read path added
in this diff never calls it.

## The bug a passing review must catch

The new read path collapses two semantically different failures into one HTTP status:

1. "The governed procedure legitimately refused this request" (e.g. cohort/target-release state
   is genuinely wrong) — a **domain refusal**, which should be a 409 the reviewer/UI can act on
   (per the pre-existing `_reject_failed_governed_receipt` pattern).
2. "Snowflake is unreachable / connection dropped / timed out" — a genuine **infrastructure**
   failure, correctly a 503.

Because `tools.py:1119-1131` turns case 1 into a bare `ValueError` with no distinguishing type or
code, and `topic_review_queue_service.py:155-157`'s `except` clause catches that same `ValueError`
type alongside the real connectivity exceptions (`ConnectionError`, `TimeoutError`), both cases
now produce the identical `TopicReviewQueueUnavailableError` → 503 response. An operator seeing
"check Snowflake configuration and reviewer access" for case 1 will investigate infrastructure
that is fine, while the actual actionable domain-state problem goes undiagnosed. The fix is not to
add a new rule — it is to route case 1 through the already-existing `_reject_failed_governed_receipt`
→ 409 pattern in the same file, which this new call site simply never wired up.

## Approved finding (what a passing answer must contain)

- Traces the full three-hop chain: `tools.py:1119-1131` (broad status-failed → bare `ValueError`)
  → `topic_review_queue_service.py:155-157` (the `except` clause that catches `ValueError`
  alongside real connectivity errors) → `app.py:237-248` (503 mapping with an infrastructure-
  flavored remedy message).
- States the concrete operator-facing consequence precisely: a genuine domain refusal surfaces as
  "check Snowflake configuration and reviewer access," sending the operator to investigate
  infrastructure that is not the problem.
- Points at the specific pre-existing fix-shaped code already in the same file —
  `_reject_failed_governed_receipt` (`:53-74`) — that the new read path should have called instead
  of falling through the broad `except`.
- States the generalizable smell: an `except (…, ValueError)` clause that spans both "the domain
  said no" and "the infrastructure is down" collapses two different HTTP statuses and two
  different operator remedies into one.
- Severity P1 — wrong operator-facing diagnosis/remedy on a governed refusal, not merely a style
  nit about broad excepts.

## Grading notes / traps

- **Fails:** flagging the broad `except (ConnectionError, TimeoutError, ValueError)` as generically
  "too broad" (a style observation) without identifying that it specifically launders a *governed
  domain refusal* into an *infrastructure* error class with the wrong remedy message.
- **Fails:** reviewing `tools.py` or `topic_review_queue_service.py` in isolation — the defect only
  becomes visible by following the exception across all three files to `app.py`'s status-code
  mapping and remedy text.
- **Partial credit:** notices the status code is likely wrong (503 vs. 409) without identifying the
  root cause (the bare `ValueError` in `tools.py` erasing the distinction) or the existing fix
  pattern (`_reject_failed_governed_receipt`) already in the file.
- **Full credit** requires citing `_reject_failed_governed_receipt` as the pattern that should have
  been reused — this is what turns the finding from "the status code seems off" into "here is the
  minimal, already-proven fix."

## Provenance

- Date: 2026-08-26
- Repo: CX Intelligence (`cx-intelligence`)
- Source artifacts: `.workflow/review.md` (Phase 4 review of "Per-Evidence Review on the Topic
  Review Queue"), citing `tools.py:1119-1131`, `topic_review_queue_service.py:53-74` and
  `:155-157`, `app.py:237-248`
- Produced by: writer model GPT-5.6 Terra (implementation); found by: strict-reviewer seat, Phase
  4 review, cycle 1
- Approved by: human, via the `workflow review` → patch-plan cycle 1 (fixed with a typed
  `GovernedProcedureFailedError` subclassing `ValueError` so pre-existing callers keep their exact
  behavior, routed through the two read-tool call sites this cycle touched; verified end-to-end
  through both FastAPI routes by cycle 2 re-review)
