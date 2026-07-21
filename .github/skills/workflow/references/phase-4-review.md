# Phase 4 — Review — `workflow review` (+ what happens after)

> ⚠️ **Invoke the `workflow` skill — do not just read this file.** If you reached this reference without invoking the `workflow` skill this turn, stop and invoke it first. These reference files are the skill's controlling contract (seat, verification bar, and the mandatory closing next-step card); reading them raw skips that contract — which is how required plan/execute steps get silently dropped.


Seat: **strict reviewer** — mapping in `ROUTING.md`. Rationale: the strongest hard-engineering
reasoner *and* an independent set of eyes — the reviewer should be a different vendor from
whichever model wrote the code; a same-vendor review is a degraded mode and should be noted as
such. A harness parallel-breadth mode is permitted if the diff is wide (many files/subsystems
reviewable independently) per the read-only-breadth invariant in `SKILL.md` — same P0–P3 output
contract, and verify its flagged signatures against real code before acting.

**Persist the verdict — review's only artifact.** Review is read-only against the code, so the
state machine can only see it happened through `.workflow/review.md`: after the review (and
after every re-review), write it — date, the HEAD commit sha reviewed, the P0–P3 findings with
dispositions, and the explicit verdict. Without this file, `workflow status` and `workflow
wrap` will correctly claim review hasn't run.

Review the changes against `.workflow/plan.md` as a strict senior engineer, including any
"Deviations" logged during execution. Verify empirically — compile, run tests, trace
producer↔consumer, run live queries. Flag: missing error handling, resource leaks, security
flaws, syntax/compile regressions, and logic that defeats the feature's own guarantees (e.g. a
gate that can never fire). Output P0 (blocker)/P1 (high)/P2 (medium)/P3 (low), and an explicit
verdict — "ship as-is" if nothing's worth acting on, otherwise the smallest disposition per issue
(fix now/defer/wontfix, one-line reason). List pre-existing/environmental failures separately so
they aren't mistaken for regressions.

If handing this to a fresh session on the strict-reviewer seat, paste:

```text
Review the changes against .workflow/plan.md as a strict senior engineer, including any "Deviations" it logged during execution. Verify empirically — compile, run tests, trace producer↔consumer, run live queries. Flag: missing error handling, resource leaks, security flaws, syntax/compile regressions, and logic that defeats the feature's own guarantees (e.g. a gate that can never fire). Output P0 (blocker) / P1 (high) / P2 (medium) / P3 (low), and an explicit verdict — "ship as-is" if there's nothing worth acting on, otherwise the smallest disposition per issue (fix now / defer / wontfix, with a one-line reason). List pre-existing/environmental failures separately so they aren't mistaken for regressions. Save the verdict, findings with dispositions, and the HEAD commit sha you reviewed to .workflow/review.md.
```

**Optional — quiz before merging:** a diff only gives a light read of what happened, since
behavior depends on existing code paths too. Before pushing anything not 100% understood, ask
one question at a time covering intent, what changed, and any non-obvious existing behavior it
now depends on.

## After Phase 4 — three outcomes

**Clean / "ship as-is":** no P0/P1/P2/P3, or nothing worth acting on. Skip straight to
`workflow wrap` — don't manufacture a patch plan for a clean review.

**P0/P1 present** (models/efforts per the patch-cycle rows in `SKILL.md`):
1. **Patch plan**: group P0/P1/P2/P3 into a file-by-file patch plan, core interfaces first, each
   with a local check — same step shape as Phase 2, including a `Skills:` line per step.
   Save to `.workflow/patch_plan.md`.
2. **Fix P0s** (review each diff): fix only the P0s, one at a time, check + commit after each.
   Don't touch P1/P2 yet.
3. **Fix P1/P2/P3s** (auto): fix the rest, verify no regressions.
4. Re-run Phase 4 review on the fixes before wrap-up. Also: every *confirmed* P0/P1 the review
   caught is a reviewer-seat golden case — tag it `[durable→eval] reviewer — ...` in
   `.workflow/learnings.md` (see `references/learning-worklog.md`) so wrap deposits the
   diff + finding before the scratch files are deleted.

**Only P2/P3 (no P0/P1):** worth a lighter patch plan — group into a file-by-file patch plan,
each with a local check AND a recommended disposition (fix now/defer/wontfix, one-line reason).
Default to the smallest safe scope. Read the dispositions and decide per item before fixing
anything — don't auto-fix everything listed, especially anything marked "defer." For items to fix
now: fix one at a time, check + commit after each; leave "defer"/"wontfix" alone (confirm the
reasoning still holds, don't implement it). Re-run Phase 4 review on the fixes before wrap-up.

Close with the next-step card (format in `SKILL.md`).
