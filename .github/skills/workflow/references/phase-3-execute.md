# Phase 3 — Execute — `workflow execute`

> ⚠️ **Invoke the `workflow` skill before acting on this file** — reading it raw is how the closing next-step card gets dropped.

Seats: pick per *step shape*, not one seat for the whole phase — mechanical → **mechanical
lane**, logic-bearing → **default executor**, schema/SQL/contract-coupled → **heavy executor**
(mapping in `ROUTING.md`). Never a parallel/sub-agent mode here — execution writes.

The loop is: baseline the test state, then **one step at a time** — load the step's `Skills:`
line, edit, check, diff, commit, persist. Four things the loop depends on and a fresh reader
won't infer:

- **The `Skills:` line is a precondition, not a hint.** Load the named skills *before* touching
  the files; they carry the repo's conventions for that kind of work. If one isn't available in
  this CLI, say so and ask rather than proceeding without it. If a step needed a skill the plan
  didn't list, or lists one that doesn't fit, use judgment and log it under "Deviations".
- **Persist after every step**, not at the end: check the step off in `.workflow/plan.md` and
  refresh its `## Execution state` block (shape defined in `references/compact.md`). This is what
  makes any compaction — planned or not — survivable; re-read that block after any of them.
- **Report progress by evidence.** Before calling a step done, audit the claim against an actual
  result from this session — a passing check, a diff, a commit. If a check failed or was skipped,
  say so plainly.
- **Deviations are logged, not improvised.** Take the conservative option, note it under
  "Deviations" in the plan, keep going. Stop outright on a failed check or unresolved file.

Append learnings as they happen (`references/learning-worklog.md`), not only at wrap.

If handing this to a fresh session, paste:

```text
Read .workflow/plan.md. First capture the baseline test state (which failures are pre-existing or environmental). Then work ONE step at a time: read the step's "Skills:" line and load/follow those skills before touching the files (if a listed skill isn't available here, say so and ask; if a step needed an unlisted skill or a listed one doesn't fit, use judgment and record it under "Deviations"). Then edit, run checks, show the diff, commit — then update .workflow/plan.md before starting the next step: check the step off and refresh the "## Execution state" section at the top (current/next step + status, baseline test results, exact in-flight signatures/schema names/contract versions, uncommitted files, pending decisions; under ~15 lines). Before reporting any step as done, audit the claim against an actual result from this session — a passing check, a diff, a commit; if something failed or is unverified, say so explicitly. If an edge case forces a deviation from the plan, take the conservative option, note it under a "Deviations" section in .workflow/plan.md, and keep going. Stop on any failed check or unresolved file. Keep contract versions in lockstep; no compat shims. Use the plan as a mutable tracker. After any context compaction (manual or automatic), re-read the "## Execution state" section before touching the next step.
```

Some harnesses offer an autonomy loop that drives a whole plan end to end without re-prompting
each step (see `ROUTING.md` harness notes) — worth knowing about, but not the default here;
only reach for it if explicitly asked, and keep review-each-diff on SQL/contract steps even
under such a loop.

**Close with the next-step card** (format in `SKILL.md`) — mandatory, no substitute. A conversational closer ("want me to proceed?") is not the card; if in doubt, print it.
