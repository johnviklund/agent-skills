# Phase 3 — Execute — `workflow execute`

> ⚠️ **Invoke the `workflow` skill — do not just read this file.** If you reached this reference without invoking the `workflow` skill this turn, stop and invoke it first. These reference files are the skill's controlling contract (seat, verification bar, and the mandatory closing next-step card); reading them raw skips that contract — which is how required plan/execute steps get silently dropped.


Seats: pick per *step shape*, not one seat for the whole phase — mechanical → **mechanical
lane**, logic-bearing → **default executor**, schema/SQL/contract-coupled → **heavy executor**
(mapping in `ROUTING.md`). Never a parallel/sub-agent mode here — execution writes.

If already in the right tool: read `.workflow/plan.md`, capture the baseline test state (which
failures are pre-existing/environmental), then work ONE step at a time — **first read the step's
`Skills:` line and load/follow the named skills before touching the files** (they carry the repo's
conventions for that kind of work); if a listed skill isn't available in this CLI, say so and ask
rather than silently proceeding without it, and if a step clearly needed a skill the plan didn't
list (or lists one that doesn't fit), apply judgment and record it under "Deviations". Then edit,
run checks, show
the diff, commit, **then persist**: check the step off in `.workflow/plan.md` and refresh its
`## Execution state` block (shape in `references/compact.md` — current/next step, baseline,
in-flight signatures/contract versions, uncommitted files, pending decisions; keep it under ~15
lines). State is written after *every* step, so any compaction — `workflow compact` or an
unplanned auto-compact — can never strand the run; after any compaction, re-read the block before
the next step. If an edge case forces a deviation, take the conservative option, note it under
a "Deviations" section in `.workflow/plan.md`, and keep going — don't silently improvise. Stop on
any failed check or unresolved file. Keep contract versions in lockstep; no compat shims. Append
this phase's learnings as they happen (see `references/learning-worklog.md`) rather than only at
the end. When `/context` shows the window filling mid-plan, run `workflow compact` (see
`references/compact.md`) between steps — never mid-step — rather than letting the CLI
auto-compact. **Report progress by evidence:** before claiming a step done, audit the claim
against an actual result from this session (a passing check, a diff, a commit) — report only
work you can point to; if a check failed or was skipped, say so plainly.

If handing this to a fresh session, paste:

```text
Read .workflow/plan.md. First capture the baseline test state (which failures are pre-existing or environmental). Then work ONE step at a time: read the step's "Skills:" line and load/follow those skills before touching the files (if a listed skill isn't available here, say so and ask; if a step needed an unlisted skill or a listed one doesn't fit, use judgment and record it under "Deviations"). Then edit, run checks, show the diff, commit — then update .workflow/plan.md before starting the next step: check the step off and refresh the "## Execution state" section at the top (current/next step + status, baseline test results, exact in-flight signatures/schema names/contract versions, uncommitted files, pending decisions; under ~15 lines). Before reporting any step as done, audit the claim against an actual result from this session — a passing check, a diff, a commit; if something failed or is unverified, say so explicitly. If an edge case forces a deviation from the plan, take the conservative option, note it under a "Deviations" section in .workflow/plan.md, and keep going. Stop on any failed check or unresolved file. Keep contract versions in lockstep; no compat shims. Use the plan as a mutable tracker. After any context compaction (manual or automatic), re-read the "## Execution state" section before touching the next step.
```

Some harnesses offer an autonomy loop that drives a whole plan end to end without re-prompting
each step (see `ROUTING.md` harness notes) — worth knowing about, but not the default here;
only reach for it if explicitly asked, and keep review-each-diff on SQL/contract steps even
under such a loop.

Close with the next-step card (format in `SKILL.md`) — mandatory when the plan finishes:
asking "want me to proceed to review?" without the card is the bug, not a substitute for it.
