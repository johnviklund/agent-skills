# Golden case — code-review — a provider response that fails adapter normalization is destroyed before it reaches the persistence boundary

## Case shape
`code-review` (seat: `strict-reviewer`)

## Input (diff / text under review)

`foundry/cx_intelligence_backend/foundry_task_evaluation.py`, the Foundry Task Evaluation Harness
(Milestone 1 restart), `FoundryStructuredOutputAdapter.complete()` (then `:634-676`) and
`_optional_nonnegative_int` (then `:1259-1264`). The spec's stated contract: "The runner persists
the raw provider result locally before validating it."

`_optional_nonnegative_int` raised `RuntimeError("Foundry returned invalid token usage metadata")`
for any non-`int`/non-`bool`/negative usage value. `complete()` also raised bare `RuntimeError` for
"no completion choice", "no structured response text", and "omitted the resolved deployment
identity" — all *after* the provider had already answered but *before* `complete()` returned a
value. `run_paired_evaluation` (`:1146-1165`) only called `write_raw_provider_result` — the only
path that persists the response text — after `complete()` returned normally. The exception path
instead called `write_raw_provider_failure` (`:1029-1044`), which persisted only
`{"error_type": type(error).__name__}` plus the original prompt, never the response the provider
actually sent back.

## The bug a passing review must catch

In a single-attempt, separately-authorized **paid** run over consumed historical evidence, a
metadata quirk with no bearing on answer quality — `usage.prompt_tokens` returned as a float, or a
missing `model` echo in the completion object — irrecoverably destroys a usable model answer. The
harness's own stated guarantee ("the runner persists the raw provider result locally before
validating it") does not hold for this entire class of failure: the raise happens strictly between
"provider answered" and "response persisted," and nothing downstream can recover the lost text
because it was never written anywhere. This is expensive specifically because the run is paid and
single-attempt — there is no retry that gets the same answer back.

## Approved finding (what a passing answer must contain)

- Locates the exact site(s): `_optional_nonnegative_int` raising on usage metadata, and the three
  `complete()` raise sites (no completion choice / no structured response text / missing resolved
  deployment identity) — all after the provider call, before `complete()` returns.
- Traces the call graph precisely: `run_paired_evaluation` only reaches
  `write_raw_provider_result` on the success path; the exception path reaches
  `write_raw_provider_failure`, which records only the error type name and the prompt, never the
  response text.
- States the concrete, non-hypothetical trigger: a *metadata* quirk (float `prompt_tokens`, a
  missing `model` echo) — not a genuine provider/content failure — is enough to lose the answer.
- Names the spec contract this violates verbatim ("persists the raw provider result locally
  before validating it") and states why the violation matters operationally: this is a
  single-attempt, separately-authorized **paid** run, so a destroyed answer cannot simply be
  retried for free.
- Severity P1 — real financial/evidence loss on a paid run, not a cosmetic or environmental issue.
- Correct fix shape (what the finding's disposition must point toward, without over-specifying
  implementation): move the normalization failure *behind* the persistence boundary — either
  return a raw-payload-carrying error/result so the raw response text is captured regardless of
  whether normalization succeeds, or persist before validating — rather than continuing to raise
  bare exceptions ahead of any capture point.

## Grading notes / traps

- **Fails:** flagging only the usage-metadata raise (`_optional_nonnegative_int`) without also
  catching the three sibling raise sites in `complete()` that share the same "after provider
  answered, before response persisted" shape — a partial catch leaves most of the actual exposure
  unaddressed.
- **Fails:** describing this as "the adapter should retry on bad metadata" — retrying does not fix
  a single-attempt paid run and misses that the real problem is *loss of data already paid for*,
  not transient failure handling.
- **Fails:** treating this as low severity because "the run still completes and reports a
  failure" — the receipt correctly shows `failed`, but the private raw evidence needed to diagnose
  or salvage the answer is gone, which is the actual harm.
- **Partial credit:** identifying the persistence-ordering bug without naming the spec contract
  line it violates, or without tying the severity explicitly to the paid/single-attempt nature of
  the run.
- **Full credit** requires (a) all four raise sites (or at minimum recognizing they share one
  shape), (b) the precise call-graph trace showing `write_raw_provider_failure` never carries the
  response text, (c) explicit citation of the spec's persist-before-validate contract, and (d) the
  paid/single-attempt severity justification.

## Provenance

- Date: 2026-09-11
- Repo: CX Intelligence (`cx-intelligence`)
- Source artifacts: `.workflow/learnings.md` (durable→eval entry), `.workflow/review.md` → Cycle 1,
  P1, `.workflow/patch_plan.md` → Steps 1–2
- Reproducer: a fake completion with `usage.prompt_tokens = -1` (or `usage.completion_tokens =
  "12"`) reached the adapter pre-fix and raised before any `raw/` write; post-fix it returns a
  `ProviderResponse` with the unusable fields set to `None`, a populated `usage_caveat`, and the
  original `response_text` intact.
- Produced by: writer model implementing the Foundry Task Evaluation Harness restart; found by:
  strict-reviewer seat, Phase 4 review, cycle 1.
- Approved by: human, via the `workflow review` → patch cycle. Fixed across Steps 1–2 of
  `.workflow/patch_plan.md` (diff `95e464c`..`3257eed`): usage-metadata failures return `None` +
  `usage_caveat` instead of raising; the remaining three normalization failures raise a dedicated
  error carrying the raw provider payload, which `run_paired_evaluation` now threads through to
  `write_raw_provider_failure` so the response text always reaches the private `raw/` artifact.
  Verified closed through cycle 3 (`Status: complete`, verdict "Ship as-is").
