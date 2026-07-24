# Mid-execution compaction — `workflow compact`

> ⚠️ **Invoke the `workflow` skill — do not just read this file.** If you reached this reference without invoking the `workflow` skill this turn, stop and invoke it first. These reference files are the skill's controlling contract (seat, verification bar, and the mandatory closing next-step card); reading them raw skips that contract — which is how required plan/execute steps get silently dropped.


**When to run what (the decision rule, also printed on every next-step card):**

- Phase boundary → `/clear` and re-ground. Never compact between phases.
- Mid-phase + `/context` filling (roughly >70%) → run `workflow compact`, between steps only.
  **The human measures** — the model can't see the meter and must not recommend compaction
  without session evidence (a harness warning, or the human's reading). ~70% is deliberate
  headroom: this command still needs room to do its reconcile before printing the line.
- Never hand-write `/compact` — the only `/compact` the human runs is the line this command
  prints.
- Window fine → do nothing.

For long Phase 3 runs (and long Phase 4 reviews) working through a multi-step plan: when the
session is filling up but the plan isn't done, don't guess at a `/compact` focus and don't let
the CLI auto-compact decide what survives. This command does two things, in this order —
**persist first, compact second** — so the durable state lives in files, where no summary,
however lossy, can destroy it. Same CLI, same model, same effort — this continues the current
phase.

**1. Reconcile `.workflow/plan.md` so the next plan step re-grounds from the file, not from
session memory.** Phase 3 already persists after every step (checklist + `## Execution state`),
so this is normally a quick verification pass — but do it, don't assume: catch anything the
per-step discipline missed before emitting any compact command:

- Confirm every completed step is checked off *and* committed (a done-but-uncommitted step is
  flagged, not checked).
- Refresh (or create) the **`## Execution state`** section at the top of the plan with exactly
  what the next step needs and a summary would mangle: the current/next step and its status;
  baseline test results (which failures are pre-existing/environmental); exact signatures,
  schema/column names, and contract versions currently in flight; files touched but not yet
  committed; and any pending decision. Keep it under ~15 lines — it's a re-ground block, not a
  transcript. This block's shape is defined here; Phase 3 maintains it per step.
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
- **Race safety.** Keep step 1 *bounded*: it's a verification pass over state that per-step
  persistence already keeps current — no large file re-reads, no content dumps. If the window
  is already critically full, skip straight to step 2 and print the line; the plan file is the
  safety net. If the harness auto-compacts before or during this command, that is the
  insurance working, not a failure: re-read the `## Execution state` block, **skip the manual
  compact line** (a second compaction right after the automatic one just burns more context),
  and continue the plan.
- **Verification-critical steps still don't compact.** If the *current* step is
  schema/SQL/contract-coupled and mid-flight, finish and commit it first, then compact; a summary
  dropping one contract version literal costs more than the tokens saved.
- Delete the `## Execution state` block at wrap-up along with the rest of the plan file — it's
  session scratch, not a durable doc.
