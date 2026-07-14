# Final check & wrap-up — `workflow wrap`

Model/effort/CLI: per the routing tables in `SKILL.md` (wrap row) — mid-tier at `medium`,
auto-approve, in either CLI. Since wrap usually follows Phase 4 in Copilot, the common move is
simply dropping Opus 4.8→Sonnet 5 after the review verdict. Rationale: wrap is procedural — the
only judgment calls are commit messages that read as worklog lines and `memory.remember`'s
routing decisions, which rules out Luna but doesn't justify Sol or Opus; the hard reasoning
already happened in review.

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
   way. Boundaries: don't add new ideas on your own initiative (it's the human's scratchpad —
   only add items the human explicitly deferred during this run, in the right section); and
   `PRODUCT.md` stays the source of truth for product state — if this run changed product
   direction, that edit goes to `PRODUCT.md`, and the TODO entry should point at it, not
   duplicate it.
6. Append this run's entry to `WORKLOG.md` (see `references/learning-worklog.md`): one capped,
   git-pointing entry, rolling the oldest off if over ~15; commit and push it with the rest.
7. Once `memory.remember` confirms every line is routed, delete `.workflow/brainstorm.md`,
   `.workflow/spec.md`, `.workflow/plan.md` (including its `## Execution state` block — session
   scratch, not a durable doc), `.workflow/patch_plan.md` (if present), and
   `.workflow/learnings.md`.

**Why this order:** commits are local — nothing leaves the machine until push. Code → learnings
routed and committed → **push** → clear scratch. Delete all the run files, not just the log —
their durable value already lives in the commits and wherever `memory.remember` routed it, and a
stale `plan.md` left behind would poison the next run's re-ground (which trusts the files as
truth). Never delete `learnings.md` before `memory.remember` has actually routed every line — it
enforces this itself, but don't race ahead of it. Open a PR only if not committing straight to
`main`. When wrap-up is done, close with the ✅ done card from `SKILL.md`, not a next-phase card.
