# Final check & wrap-up — `workflow wrap`

> ⚠️ **Invoke the `workflow` skill before acting on this file** — reading it raw is how the closing next-step card gets dropped.

Seat: wrap runs on a mid-tier seat at medium effort, auto-approve, in either harness — mapping
and the practical after-review model swap in `ROUTING.md`. Rationale: wrap is procedural — the
only judgment calls are commit messages that read as worklog lines and `memory.remember`'s
routing decisions, which rules out the mechanical lane but doesn't justify a heavy or reviewer
seat; the hard reasoning already happened in review.

**Precondition — review evidence.** Before anything else, confirm `.workflow/review.md` exists,
its verdict is clean or every finding is dispositioned, and it names the current HEAD (no code
commits after the reviewed sha). If it's missing or stale, don't lecture that review "hasn't
run" — it may have run before this artifact existed — say the evidence is missing/stale and
print the next-step card routing to `workflow review`.

**Escalate on failure, don't fix in wrap:** if step 1's final checks surface a regression, stop —
that's a mini review→patch cycle (route it through the patch-cycle models in `SKILL.md`), not
something to patch inline at wrap's effort/approval settings.

Commit, push, curate, and clean up — in one go:

1. Run final checks: build, type-check, full test suite (call out known-environmental failures,
   don't treat them as regressions).
2. Grep for leftover shortcuts: `TODO: Implement`, `NotImplementedError`, `...`, `placeholder`,
   `real implementation`, and any old contract version literal — nothing should still pin it.
3. Commit any remaining changes. **Commit discipline:** use session-level messages that read as a
   worklog line on their own (what shipped + why), not terse "fix" stubs — the commit log is the
   portable backtrack record, so make it carry the narrative.
4. Invoke `memory.remember` to route every tagged line in `.workflow/learnings.md` to its
   destination (`MEMORY.md`, `AGENTS.md`, `README.md`, an existing or new skill, `DESIGN.md`),
   commit those changes, and push.
5. **TODO hygiene** — update repo-root `TODO.md` (if present) from the plan's `## TODO impacts`
   list plus anything done in passing: move completed/subsumed items to Archived with a one-line
   pointer (commit sha or the initiative that subsumed them); rewrite items whose scope this run
   changed so they match the code that now exists; check off Small UI Changes shipped along the
   way; if `ROADMAP.md` exists, check off items this run completed (pointer updates only —
   same boundaries). Boundaries: don't add new ideas on your own initiative (it's the human's scratchpad —
   only add items the human explicitly deferred during this run, in the right section); and
   `PRODUCT.md` stays the source of truth for product state — if this run changed product
   direction, that edit goes to `PRODUCT.md`, and the TODO entry should point at it, not
   duplicate it.
6. **Eval deposit** — for every `[durable→eval]` line in `.workflow/learnings.md`, write a
   golden case to `evals/<seat>/<YYYY-MM-DD>-<slug>.md` in the repo (seats: `spec`, `plan`,
   `reviewer`, `mechanical`). Each case must be self-contained, because the source artifacts are
   gitignored scratch about to be deleted in step 8 — **copy content in, don't point at
   `.workflow/` paths**: the input (e.g. the brainstorm text, the spec, the diff), the approved
   output, grading notes (what a passing answer must contain, known traps), and provenance
   (date, commit shas, which model produced and which approved it). Enforce the admission test
   and the ~15-per-seat rolling cap from `references/learning-worklog.md` — displace the
   weakest case when full, never append past the cap. Commit with the rest.
7. Append this run's entry to `WORKLOG.md` (see `references/learning-worklog.md`): one capped,
   git-pointing entry, rolling the oldest off if over ~15; commit and push it with the rest.
8. Once `memory.remember` confirms every line is routed and step 6's cases are deposited,
   delete `.workflow/brainstorm.md`,
   `.workflow/spec.md`, `.workflow/plan.md` (including its `## Execution state` block — session
   scratch, not a durable doc), `.workflow/patch_plan.md` and `.workflow/review.md` (if
   present), and `.workflow/learnings.md`.

**Why this order:** commits are local — nothing leaves the machine until push. Code → learnings
routed, evals deposited, and committed → **push** → clear scratch. Delete all the run files, not
just the log —
their durable value already lives in the commits, the `evals/` cases, and wherever
`memory.remember` routed it, and a
stale `plan.md` left behind would poison the next run's re-ground (which trusts the files as
truth). Never delete `learnings.md` before `memory.remember` has actually routed every line — it
enforces this itself, but don't race ahead of it. Open a PR only if not committing straight to
`main`. When wrap-up is done, close with the ✅ done card from `SKILL.md`, not a next-phase card.
