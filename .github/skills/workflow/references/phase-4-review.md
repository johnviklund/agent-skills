# Phase 4 — Review — `workflow review` (+ what happens after)

> ⚠️ **Invoke the `workflow` skill before acting on this file** — reading it raw is how the closing next-step card gets dropped.

Seat: **strict reviewer** — mapping in `ROUTING.md`. Rationale: the strongest hard-engineering
reasoner *and* an independent set of eyes — the reviewer should be a different vendor from
whichever model wrote the code. Read the plan's model-only writer fields and resolve independence
through `ROUTING.md`, without copying any harness or provider name into an artifact. A harness
parallel-breadth mode is permitted on a wide diff per `SKILL.md`'s read-only-breadth invariant —
same P0–P3 output contract either way.

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

Run it directly, or hand it to a fresh session on the strict-reviewer seat by pasting:

```text
Review the changes against .workflow/plan.md as a strict senior engineer, including any "Deviations" it logged during execution. Read its model-only writer fields and use ROUTING.md to determine whether the review is cross-vendor or same-vendor; record only that generic independence status, never a harness, provider, vendor, or coding-agent product name. Verify empirically — compile, run tests, trace producer↔consumer, run live queries. Flag: missing error handling, resource leaks, security flaws, syntax/compile regressions, and logic that defeats the feature's own guarantees (e.g. a gate that can never fire). Output P0 (blocker) / P1 (high) / P2 (medium) / P3 (low), and an explicit verdict — "ship as-is" if there's nothing worth acting on, otherwise the smallest disposition per issue (fix now / defer / wontfix, with a one-line reason). List pre-existing/environmental failures separately so they aren't mistaken for regressions. Write .workflow/review.md BEFORE you start reviewing, with a five-line provenance header — Command, Created (date), Base (the HEAD sha reviewed), Inputs (.workflow/plan.md @ its own Base sha), Status (drafting) — then a "## Coverage" checklist of every area you intend to review, an "Independence:" line, and a "## Cycle 1 findings" heading. Append each finding to that section the moment you confirm it, with its evidence and disposition, and tick the Coverage entry as you finish each area; do not hold findings in context to write up at the end. List pre-existing/environmental failures under "## Pre-existing / environmental". Write the "## Cycle 1 verdict" section last, then set Status to complete. If you are resuming a drafting review.md, continue from the first unticked Coverage entry rather than starting over.
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
   with a local check — same step shape as Phase 2, including a `Skills:` line per step. Scope
   each check to exactly what that step changes (the phrase removed, the phrase added), not a
   global count of a substring that can legitimately appear elsewhere (e.g. reserve `wc -l` for a
   file with a real line budget, not as a stand-in for "did the edit land"); before writing a
   "count is 0" check, grep the plan's own other steps for a collision. A check that stops a
   correct edit costs a whole cycle. Save to `.workflow/patch_plan.md`.
2. **Fix P0s** (review each diff): fix only the P0s, one at a time, check + commit after each.
   Don't touch P1/P2 yet.
3. **Fix P1/P2/P3s** (auto): fix the rest, verify no regressions.
4. Re-run Phase 4 review on the fixes before wrap-up. Also: every *confirmed* P0/P1 the review
   caught is a reviewer-seat golden case — tag it `[durable→eval] code-review — ...` in
   `.workflow/learnings.md` (see `references/learning-worklog.md`) so wrap deposits the
   diff + finding before the scratch files are deleted.

**Only P2/P3 (no P0/P1):** worth a lighter patch plan — group into a file-by-file patch plan,
each with a local check AND a recommended disposition (fix now/defer/wontfix, one-line reason).
Default to the smallest safe scope. Read the dispositions and decide per item before fixing
anything — don't auto-fix everything listed, especially anything marked "defer." For items to fix
now: fix one at a time, check + commit after each; leave "defer"/"wontfix" alone (confirm the
reasoning still holds, don't implement it). Re-run Phase 4 review on the fixes before wrap-up.

**Close with the next-step card** (format in `SKILL.md`) — mandatory, no substitute. A conversational closer ("want me to proceed?") is not the card; if in doubt, print it.
