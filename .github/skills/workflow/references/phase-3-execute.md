# Phase 3 — Execute — `workflow execute`

> ⚠️ **Invoke the `workflow` skill before acting on this file** — reading it raw is how the closing next-step card gets dropped.

Seats: pick per *step shape*, not one seat for the whole phase — mechanical → **mechanical
lane**, logic-bearing → **default executor**, schema/SQL/contract-coupled → **heavy executor**
(mapping in `ROUTING.md`). Never a parallel/sub-agent mode here — execution writes.

**Which checklist:** `.workflow/<slug>/patch_plan.md` if it exists with unchecked steps — the
original checklist is complete and is never re-run; otherwise `plan.md`. Everything below applies
to whichever file is live, including its own `## Execution state` block. When the last patch step
is ticked, the next step is review cycle N+1, not wrap.

**Freshness before the first edit** (and again whenever resuming from a fresh context): run
`git diff --stat <checklist Base>..HEAD -- <every file the checklist names>`. The only acceptable
changes are this run's own step commits, which the execution state lists as `Step N @ <sha>`.
Anything else — a commit that touched a target file while the run was parked, or between plan
and execute — means the audit is stale: stop and route to `workflow plan <slug>` (or
`workflow review <slug>` for a patch plan). Ancestry is not freshness.

The loop is: baseline the test state using the commands in `AGENTS.md`'s *Verifying your work*
block (if the block is missing, stop and ask for it rather than guessing at commands), then **one
step at a time** — read the step's `Skills:`
files, edit, check, diff, commit, persist. Five things the loop depends on and a fresh reader
won't infer:

- **Scope lock — the step is the whole job.** The plan was audited by a stronger seat; execution
  does not re-audit it, re-verify its findings, or re-read the spec and brainstorm for context.
  Touch only what the step names; no adjacent refactors, no extra tests beyond the `Check:`, no
  "while I'm here" fixes. Something wrong *outside* the step is a one-line note under
  "Deviations" and the step continues; something wrong *inside* it that the plan didn't foresee
  is a deviation taken conservatively — or a stop, if it changes an interface or a contract.
  Neither is a reason to widen the step.

- **The `Skills:` line is a precondition, not a hint.** Read the listed `SKILL.md` files *before*
  touching the code; they carry the repo's conventions for that kind of work. If a listed path
  doesn't resolve here, say so and ask rather than proceeding without it. If a step needed a skill the plan
  didn't list, or lists one that doesn't fit, use judgment and log it under "Deviations".
- **Persist after every step**, not at the end: check the step off in `.workflow/<slug>/plan.md`, append
  `- Writer: <vendor> · <model>` under the step you just completed, and refresh the plan's
  `## Execution state` block at the top of the file — the current/next step and its status; one
  `Step N @ <sha>` line per committed step (the freshness check reads these); this run's
  `writer: <vendor> · <model> (self-declared)`; baseline test results (which failures are
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
- **Report progress by evidence, in three lines.** Before calling a step done, audit the claim
  against an actual result from this session — a passing check, a diff, a commit. If a check
  failed or was skipped, say so plainly. Per step the chat receipt is: `Step N — <what>` ·
  `Check: <pass/fail + the one number that proves it>` · `Commit: <sha>` — the diff is shown for
  review where approval requires it, nothing else is narrated.
- **Deviations are logged, not improvised.** Take the conservative option, note it under
  "Deviations" in the plan, keep going. Stop outright on a failed check or unresolved file; a
  check command that is not found (exit 127) is a failed check, and a check that cannot be
  satisfied as written is a deviation to report, never a number to adjust the text toward.

Append learnings as they happen (`references/learning-worklog.md`), not only at wrap.

If handing this to a fresh session, paste:

```text
Read .workflow/<slug>/patch_plan.md if it exists and has unchecked steps, otherwise .workflow/<slug>/plan.md (<slug> is the run named in my command) — that file is your whole brief and the only thing you read under .workflow/; do not re-audit it, re-verify its findings, or re-read spec/brainstorm for context. Before any edit, check freshness: git diff --stat <its Base>..HEAD -- <every file its steps name> must show only commits listed as "Step N @ <sha>" in its Execution state; anything else means stop and say the plan needs re-auditing. If its header says Status: drafting, stop and say so — the plan is unfinished and must not be executed. Otherwise first capture the baseline test state using the build/test/lint commands in AGENTS.md's "Verifying your work" block (if that block is missing, stop and ask for it rather than guessing) — note which failures are pre-existing or environmental. A failing check is fixed in the code, never by editing or deleting the test. Then work ONE step at a time, touching only what the step names — no adjacent refactors, no tests beyond the step's Check, no fixes outside the step (note those in one line under "Deviations" and keep going): read the step's "Skills:" line and read/follow those SKILL.md files before touching the code (if a listed path doesn't resolve here, say so and ask; if a step needed an unlisted skill or a listed one doesn't fit, use judgment and record it under "Deviations"). Then edit, run checks, show the diff, commit — then update .workflow/<slug>/plan.md before starting the next step: check the step off, append "- Writer: <vendor> · <model>" under it naming the model vendor and model that executed it, and refresh the "## Execution state" section at the top (current/next step + status, this run's writer, baseline test results, exact in-flight signatures/schema names/contract versions, uncommitted files, pending decisions; under ~15 lines). After each commit add "Step N @ <sha>" to the Execution state. Before reporting any step as done, audit the claim against an actual result from this session — a passing check, a diff, a commit; if something failed or is unverified, say so explicitly. Report each step in three lines — Step N — what · Check: pass/fail with the one number that proves it · Commit: sha — and show the diff only where the step's approval level requires it; do not narrate file reads, restate the plan, or summarize at the end. If an edge case forces a deviation from the plan, take the conservative option, note it under a "Deviations" section in .workflow/<slug>/plan.md, and keep going. Stop on any failed check or unresolved file. Keep contract versions in lockstep; no compat shims. Use the plan as a mutable tracker. After any context compaction (manual or automatic), re-read the "## Execution state" section before touching the next step.
```

Some CLIs offer an autonomy loop that drives a whole plan end to end without re-prompting each
step (see `ROUTING.md`'s autonomy notes) — worth knowing about, but not the default here;
only reach for it if explicitly asked, and keep review-each-diff on SQL/contract steps even
under such a loop.

**Close with the next-step card** (format in `SKILL.md`) — mandatory, no substitute. Read `ROUTING.md` now and fill row 2 with the next seat's concrete vendor · model · effort · context window and first fallback; a seat name or "see ROUTING.md" is a defect. A conversational closer ("want me to proceed?") is not the card; if in doubt, print it.
