# Phase 4 — Review — `workflow review` (+ what happens after)

> ⚠️ **Invoke the `workflow` skill before acting on this file** — reading it raw is how the closing next-step card gets dropped.

Seat: **strict reviewer** — mapping in `ROUTING.md`. Rationale: the strongest hard-engineering
reasoner *and* an independent set of eyes — the reviewer should be a different vendor from
whichever model wrote the code. Don't wait to be told which that was — read the plan's
`## Execution state` `writer` field and its per-step writers, and if your own vendor matches, this
is a degraded same-vendor review. A CLI's parallel-breadth mode is permitted on a wide diff
per `SKILL.md`'s read-only-breadth invariant — same P0–P3 output contract either way.

**Persist as you go — `review.md` is review's only artifact.** Review is read-only against the
code, so the state machine can only see it happened through `.workflow/review.md`, and a finding
held in context is one context death from gone. Open the file *before* reviewing anything, with
`Status: drafting`, and grow it in place:

```markdown
## Coverage
- [x] plan.md steps 1–6 + Deviations
- [x] src/domain/mission.ts
- [ ] src/app/api/…
Independence: cross-vendor | same-vendor (degraded)

## Cycle 1 findings
### P1 — <title>
- Evidence: <file:line>, <check output>
- Disposition: fix now | defer | wontfix — <reason>

## Pre-existing / environmental

## Cycle 1 verdict          <- written last; flips Status to complete
```

Three rules make this work:

- **Append each finding the moment it's confirmed**, before moving to the next area — not in one
  pass at the end.
- **`## Coverage` is the re-ground block.** Tick each area as it's reviewed. A reset mid-review
  then costs the diff already read, not the findings; the next session resumes at the first
  unchecked entry instead of starting over. Same role `## Execution state` plays for Phase 3.
- **Re-reviews append a new `## Cycle N` section; they never overwrite.** The escalation contract
  below has to state what was attempted across the cycles and why each attempt failed — if each
  cycle overwrote the last, that history would live only in context, which is exactly the
  dependency this removes. `Status` returns to `drafting` when a cycle opens.

Without this file, `workflow status` and `workflow wrap` will correctly claim review hasn't run.

**P3 is capped; noise is excluded.** Report at most five P3s and summarize the rest as a count;
the count is enough for a disposition of "defer". Do not report generated paths, anything a
linter or CI check already enforces, or style and naming — those are not findings. **A repeat is
memory.** Before recording a P0–P2, check `MEMORY.md` and `evals/strict-reviewer/` for the same
class of mistake; a second occurrence is tagged `[durable→memory]` in `.workflow/learnings.md`
as part of the review, so the next run's grounding carries it and the class is caught earlier.

**The chat receipt is the verdict, not the findings.** Findings live in `review.md`; the turn
reports the verdict, a count per severity, the P0/P1 titles in one line each, and any decision the
human owes (dispositions on P2/P3 as a lettered list with recommended defaults) — then the card.

Run it directly, or hand it to a fresh session on the strict-reviewer seat by pasting:

```text
Review the changes against .workflow/plan.md as a strict senior engineer, including any "Deviations" it logged during execution. First read the plan's "## Execution state" writer field and the per-step "Writer:" lines; if your own vendor wrote this code, this is a degraded same-vendor review and you must say so in review.md. Verify empirically — compile, run tests, trace producer↔consumer, run live queries. Flag: missing error handling, resource leaks, security flaws, syntax/compile regressions, and logic that defeats the feature's own guarantees (e.g. a gate that can never fire). Output P0 (blocker) / P1 (high) / P2 (medium) / P3 (low), and an explicit verdict — "ship as-is" if there's nothing worth acting on, otherwise the smallest disposition per issue (fix now / defer / wontfix, with a one-line reason). Report at most five P3s and summarize any further ones as a count; skip generated paths, anything lint/CI already enforces, and style or naming. For every P0–P2, check MEMORY.md and evals/strict-reviewer/ for the same class of mistake; if it has occurred before, add a "[durable→memory]" line to .workflow/learnings.md as part of this review. List pre-existing/environmental failures separately so they aren't mistaken for regressions. Write .workflow/review.md BEFORE you start reviewing, with a five-line provenance header — Command, Created (date), Base (the HEAD sha reviewed), Inputs (.workflow/plan.md @ its own Base sha), Status (drafting) — then a "## Coverage" checklist of every area you intend to review plus an "Independence:" line, then a "## Cycle 1 findings" heading. Append each finding to that section the moment you confirm it, with its evidence and disposition, and tick the Coverage entry as you finish each area; do not hold findings in context to write up at the end. List pre-existing/environmental failures under "## Pre-existing / environmental". Write the "## Cycle 1 verdict" section last, then set Status to complete. In chat report only the verdict, a count per severity, one line per P0/P1, and any dispositions you need from me as a lettered list with your recommended default marked. If you are resuming a drafting review.md, continue from the first unticked Coverage entry rather than starting over.
```

**Optional — quiz before merging:** a diff only gives a light read of what happened, since
behavior depends on existing code paths too. Before pushing anything not 100% understood, ask
one question at a time covering intent, what changed, and any non-obvious existing behavior it
now depends on.

## After Phase 4 — three outcomes

**Cycle bound — three, then stop.** One cycle = patch plan → fixes → re-review. Both patched
outcomes below end in a re-review, and that re-review may open the next cycle — it may not open a
fourth. Stop and escalate to the human, without starting another cycle, as soon as either is true:
three cycles have completed, or a P0 survived a cycle unresolved (fixed and still found, or its fix
failed its own check). The escalation states the unresolved finding verbatim, what was attempted
across the cycles and why each attempt failed, and one exact answerable question — then closes with
the next-step card routed to the human. Never a fourth blind cycle.

**Clean / "ship as-is":** no P0/P1/P2/P3, or nothing worth acting on. Skip straight to
`workflow wrap` — don't manufacture a patch plan for a clean review.

**P0/P1 present** (models/efforts per the patch-cycle rows in `ROUTING.md`):
1. **Patch plan**: group P0/P1/P2/P3 into a file-by-file patch plan, core interfaces first, each
   with a local check — same step shape as Phase 2, including a `Skills:` line per step. **A P0/P1
   that is a behavioral bug gets two steps, not one:** first a test that reproduces it — run, seen
   to fail for the expected reason, committed on its own; then the fix, which may not edit that
   test or any other test file. The committed failing test is the proof the bug is gone, and a
   fix that touches tests is rejected at re-review. Scope
   each check to exactly what that step changes (the phrase removed, the phrase added), not a
   global count of a substring that can legitimately appear elsewhere (e.g. reserve `wc -l` for a
   file with a real line budget, not as a stand-in for "did the edit land"); before writing a
   "count is 0" check, grep the plan's own other steps for a collision. A check that stops a
   correct edit costs a whole cycle. Save to `.workflow/patch_plan.md`.
2. **Fix P0s** (review each diff): fix only the P0s, one at a time, check + commit after each.
   Don't touch P1/P2 yet.
3. **Fix P1/P2/P3s** (auto): fix the rest, verify no regressions.
4. Re-run Phase 4 review on the fixes before wrap-up — and on a bug fix, confirm the fix commit
   touched no test file and the reproducing test now passes. Also: a *confirmed* P0/P1 that the
   writer missed is a reviewer-exam case only if it passes the admission test in
   `references/learning-worklog.md` (most don't) — tag it `[durable→eval] code-review — ...` in
   `.workflow/learnings.md` so wrap deposits the diff + finding before scratch is cleared.

**Only P2/P3 (no P0/P1):** worth a lighter patch plan — group into a file-by-file patch plan,
each with a local check AND a recommended disposition (fix now/defer/wontfix, one-line reason).
Default to the smallest safe scope. Read the dispositions and decide per item before fixing
anything — don't auto-fix everything listed, especially anything marked "defer." For items to fix
now: fix one at a time, check + commit after each; leave "defer"/"wontfix" alone (confirm the
reasoning still holds, don't implement it). Re-run Phase 4 review on the fixes before wrap-up.

**Close with the next-step card** (format in `SKILL.md`) — mandatory, no substitute. A conversational closer ("want me to proceed?") is not the card; if in doubt, print it.
