# Mid-execution compaction — `workflow compact`

For long Phase 3 runs (and long Phase 4 reviews) working through a multi-step plan: when the
session is filling up but the plan isn't done, don't guess at a `/compact` focus and don't let
the CLI auto-compact decide what survives. This command does two things, in this order —
**persist first, compact second** — so the durable state lives in files, where no summary,
however lossy, can destroy it. Same CLI, same model, same effort — this continues the current
phase.

**1. Update `.workflow/plan.md` so the next plan step re-grounds from the file, not from
session memory.** Before emitting any compact command:

- Check off every completed step; confirm each was committed (a done-but-uncommitted step is
  flagged, not checked).
- Refresh (or create) an **`## Execution state`** section at the top of the plan with exactly
  what the next step needs and a summary would mangle: the current/next step and its status;
  baseline test results (which failures are pre-existing/environmental); exact signatures,
  schema/column names, and contract versions currently in flight; files touched but not yet
  committed; and any pending decision. Keep it under ~15 lines — it's a re-ground block, not a
  transcript.
- Append any unrecorded deviations to the plan's "Deviations" section and any unrecorded
  learnings to `.workflow/learnings.md` (see `references/learning-worklog.md`) — both are
  casualties of a careless compact otherwise.

**2. Hand back the optimized compact line, ready to paste.** Derive the focus from the plan
state just written — name the *specific* things to keep, in this shape:

```text
/compact keep: current plan step (<step name>) and its status, baseline test results, exact signatures/contract versions from .workflow/plan.md "Execution state", uncommitted diff for <files>. Drop: old file dumps, resolved plan steps, exploration that led to already-committed work.
```

The skill can't run the CLI's `/compact` itself — it prints the line and the human runs it. End
the response with that line as the paste block of the next-step card.

## Guardrails

- **Only mid-phase.** At a phase boundary (plan finished, moving 3→4), `/clear` + re-ground is
  strictly better — refuse politely and print the normal next-step card instead.
- **Auto-compact insurance.** Because step 1 writes state to `.workflow/plan.md` *before* any
  compaction, even an unplanned CLI auto-compact mid-run recovers cleanly: the standing rule is
  re-read the plan's `Execution state` block after *any* compaction, manual or automatic, before
  touching the next step. Run `workflow compact` proactively when `/context` shows the window
  filling — before the auto-compact fires, not after.
- **Verification-critical steps still don't compact.** If the *current* step is
  schema/SQL/contract-coupled and mid-flight, finish and commit it first, then compact; a summary
  dropping one contract version literal costs more than the tokens saved.
- Delete the `## Execution state` block at wrap-up along with the rest of the plan file — it's
  session scratch, not a durable doc.
