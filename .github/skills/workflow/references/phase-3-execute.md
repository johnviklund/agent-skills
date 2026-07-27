# Phase 3 — Execute — `workflow execute`

> ⚠️ **Invoke the `workflow` skill before acting on this file** — reading it raw is how the closing next-step card gets dropped.

Seats: pick per *step shape*, not one seat for the whole phase — mechanical → **mechanical
lane**, logic-bearing → **default executor**, schema/SQL/contract-coupled → **heavy executor**
(mapping in `ROUTING.md`). Never a parallel/sub-agent mode here — execution writes.

The loop is: baseline the test state, then **one step at a time** — read the step's `Skills:`
files, edit, check, diff, commit, persist. Four things the loop depends on and a fresh reader
won't infer:

- **The `Skills:` line is a precondition, not a hint.** Read the listed `SKILL.md` files *before*
  touching the code; they carry the repo's conventions for that kind of work. If a listed path
  doesn't resolve here, say so and ask rather than proceeding without it. If a step needed a skill the plan
  didn't list, or lists one that doesn't fit, use judgment and log it under "Deviations".
- **Persist after every step**, not at the end: check the step off in `.workflow/plan.md`, append
  `- Writer: <harness> · <model>` under the step you just completed, and refresh the plan's
  `## Execution state` block at the top of the file — the current/next step and its status; this
  run's `writer: <harness> · <model> (self-declared)`; baseline test results (which failures are
  pre-existing/environmental); exact signatures, schema/column names and contract versions in
  flight; files touched but not yet committed; any pending decision. Keep it under ~15 lines: a
  re-ground block, not a transcript. This is what makes a reset — or an unplanned auto-compaction
  — survivable; re-read the block after either, before touching the next step. The per-step writer
  is what lets Phase 4 detect a same-vendor review without being told.
- **Low on context? Reset, don't summarize.** State is on disk after every step, so a reset costs
  warm cache and nothing else — and unlike compaction it is safe at *any* fullness, needing no
  headroom to perform. Before resetting: finish or abandon the current step, check it off only if
  it is also committed (done-but-uncommitted is flagged, not checked), and append any unrecorded
  deviations and learnings (`references/learning-worklog.md`). If the current step is
  schema/SQL/contract-coupled and mid-flight, finish and commit it first — re-deriving one
  contract version literal costs more than the reset saved.
- **Report progress by evidence.** Before calling a step done, audit the claim against an actual
  result from this session — a passing check, a diff, a commit. If a check failed or was skipped,
  say so plainly.
- **Deviations are logged, not improvised.** Take the conservative option, note it under
  "Deviations" in the plan, keep going. Stop outright on a failed check or unresolved file.

Append learnings as they happen (`references/learning-worklog.md`), not only at wrap.

If handing this to a fresh session, paste:

```text
Read .workflow/plan.md. If its header says Status: drafting, stop and say so — the plan is unfinished and must not be executed. Otherwise first capture the baseline test state (which failures are pre-existing or environmental). Then work ONE step at a time: read the step's "Skills:" line and read/follow those SKILL.md files before touching the code (if a listed path doesn't resolve here, say so and ask; if a step needed an unlisted skill or a listed one doesn't fit, use judgment and record it under "Deviations"). Then edit, run checks, show the diff, commit — then update .workflow/plan.md before starting the next step: check the step off, append "- Writer: <harness> · <model>" under it naming the harness and model that executed it, and refresh the "## Execution state" section at the top (current/next step + status, this run's writer, baseline test results, exact in-flight signatures/schema names/contract versions, uncommitted files, pending decisions; under ~15 lines). Before reporting any step as done, audit the claim against an actual result from this session — a passing check, a diff, a commit; if something failed or is unverified, say so explicitly. If an edge case forces a deviation from the plan, take the conservative option, note it under a "Deviations" section in .workflow/plan.md, and keep going. Stop on any failed check or unresolved file. Keep contract versions in lockstep; no compat shims. Use the plan as a mutable tracker. After any context compaction (manual or automatic), re-read the "## Execution state" section before touching the next step.
```

Some harnesses offer an autonomy loop that drives a whole plan end to end without re-prompting
each step (see `ROUTING.md` harness notes) — worth knowing about, but not the default here;
only reach for it if explicitly asked, and keep review-each-diff on SQL/contract steps even
under such a loop.

**Close with the next-step card** (format in `SKILL.md`) — mandatory, no substitute. A conversational closer ("want me to proceed?") is not the card; if in doubt, print it.
