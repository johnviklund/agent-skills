# Final check & wrap-up — `workflow wrap`

> ⚠️ **Invoke the `workflow` skill before acting on this file** — reading it raw is how the closing next-step card gets dropped.

Seat: wrap runs on a mid-tier seat at medium effort, auto-approve, in either harness — mapping
and the practical after-review model swap in `ROUTING.md`. Rationale: wrap is procedural — the
only judgment calls are commit messages that read as worklog lines and `memory.remember`'s
routing decisions, which rules out the mechanical lane but doesn't justify a heavy or reviewer
seat; the hard reasoning already happened in review.

**Precondition — review evidence.** Before anything else, confirm `.workflow/review.md` exists with
`Status: complete`, its verdict is clean or every finding is dispositioned, and its `Base` is the current HEAD (no code
commits after the reviewed sha). If it's missing or stale, don't lecture that review "hasn't
run" — it may have run before this artifact existed — say the evidence is missing/stale and
print the next-step card routing to `workflow review`.

**Precondition — clean code tree.** Then run `git status --porcelain`. Every modified or untracked
path it reports must fall inside this allowlist: `TODO.md`, `MEMORY.md`, `WORKLOG.md`, `ROADMAP.md`,
`PRODUCT.md`, `DESIGN.md`, `docs/`, `evals/`, `.gitignore`, `.workflow/` (which shows up here in
repos that track it). Anything outside it is code the review verdict never saw — stop,
name the offending paths, and print the next-step card routing to `workflow review`. Wrap does not
launder unreviewed code through its own commit. Dirt *inside* the allowlist is wrap's normal input
and gets committed in step 3.

**Escalate on failure, don't fix in wrap:** if step 1's final checks surface a regression, stop —
that's a mini review→patch cycle (route it through the patch-cycle rows in `ROUTING.md`), not
something to patch inline at wrap's effort/approval settings.

Commit, push, curate, and clean up — in one go:

1. Run final checks: build, type-check, full test suite (call out known-environmental failures,
   don't treat them as regressions).
2. Grep for leftover shortcuts: `TODO: Implement`, `NotImplementedError`, `...`, `placeholder`,
   `real implementation`, and any old contract version literal — nothing should still pin it.
3. Commit the remaining changes — which the entry gate has already narrowed to allowlisted
   doc/scratch paths. **Commit discipline:** use session-level messages that read as a
   worklog line on their own (what shipped + why), not terse "fix" stubs — the commit log is the
   portable backtrack record, so make it carry the narrative.
4. Invoke `memory.remember` to route every tagged line in `.workflow/learnings.md` to its
   destination (`MEMORY.md`, `AGENTS.md`, `README.md`, an existing or new skill, `DESIGN.md`),
   commit those changes, and push.
5. **Product-doc truth** — answer this explicitly; silence is not an answer. Start from the plan's
   `## Product doc impacts`, then re-derive it from what actually shipped: deviations and patch
   cycles change scope after Phase 2, so the plan's list is the starting point, not the verdict.
   For each of `PRODUCT.md`, `DESIGN.md` and `ROADMAP.md` that exists, state either "no statement
   changed" or the edit made — a stale statement of current state/scope/stack corrected, an open
   decision this run resolved moved out of the open list and recorded as decided, a completed
   roadmap item checked off. **Include the decisions nobody listed**: shipping is how most product
   decisions actually get made, and one settled in passing and never written down is the commonest
   way `PRODUCT.md` goes stale. If this run fixed how something works, that answer belongs in
   `PRODUCT.md` whether or not anyone had thought to ask the question first.
   **Never rewrite a settled principle or a stated boundary to match the
   code**: that inverts the source of truth. Stop, name the contradiction, and print the card
   routing it to the human as a product decision — the code may be the thing that's wrong. Commit
   doc edits before step 6 so `TODO.md` points at truth rather than duplicating a stale claim.
6. **TODO hygiene** — update repo-root `TODO.md` (if present) from the plan's `## TODO impacts`
   list plus anything done in passing: move completed/subsumed items to Archived with a one-line
   pointer (commit sha or the initiative that subsumed them); rewrite items whose scope this run
   changed so they match the code that now exists; check off Small UI Changes shipped along the
   way. Roadmap status belongs to step 5, not here.

   Then reconcile the two, so intake and committed direction can't drift apart: no open `TODO.md`
   item may duplicate an active `ROADMAP.md` item — point it at the roadmap item or archive it;
   every roadmap item step 5 just checked off has its `TODO.md` entries archived with a pointer to
   that item; and a roadmap item this run descoped or abandoned lands back in `TODO.md` as a
   deferred entry, naming what it was and why it stopped. A committed item may leave the roadmap,
   but it never simply disappears.

   Boundaries: don't add new ideas on your own initiative (it's the human's scratchpad —
   only add items the human explicitly deferred during this run, in the right section); and the
   TODO entry points at the product docs step 5 just corrected, it never duplicates them.
7. **Eval deposit** — for every `[durable→eval]` line in `.workflow/learnings.md`, resolve its
   shape to a seat via the case-shape table in `references/learning-worklog.md`, then write a
   golden case to `evals/<seat>/<shape>-<YYYY-MM-DD>-<slug>.md` in the repo. Each case must be
   self-contained, because the source artifacts are run scratch about to be cleared in step 9 — **copy content in, don't point at
   `.workflow/` paths**: the input (e.g. the brainstorm text, the spec, the diff), the approved
   output, grading notes (what a passing answer must contain, known traps), and provenance
   (date, commit shas, and which model produced and approved it; never a harness, provider,
   vendor, or coding-agent product name). Enforce the admission test
   and the ~15-per-shape rolling cap from `references/learning-worklog.md` — displace the
   weakest case of the same shape when full, never append past the cap. Commit with the rest.
8. Append this run's entry to `WORKLOG.md` (see `references/learning-worklog.md`): one capped,
   git-pointing entry, rolling the oldest off if over ~15; commit and push it with the rest.
9. **Clear the run — by inventory, not by list.** Once `memory.remember` confirms every line is
   routed and step 7's cases are deposited, enumerate **everything** this run left in
   `.workflow/` — `git status --porcelain .workflow/` for the untracked/modified side, `git
   ls-files .workflow/` for the tracked side, and a plain recursive listing to catch what both
   miss. Phase artifacts are only part of it: runs also leave receipts, verification JSON, live
   harnesses, and one-off import/migration scripts, and a fixed six-file delete list is why those
   accumulate forever. Every path gets exactly one disposition from the table below; anything you
   cannot confidently classify is **reported to the human, not guessed at**.

   **9a. Reference check — before any move or delete, no exceptions.** For each candidate path,
   grep the repo *outside* `.workflow/` for both its filename and its bare module name (the stem
   without `.py`/`.md`) — e.g. `rg -n -F '<stem>' --glob '!.workflow/**'`. That catches committed
   tests that do `sys.path.insert(0, ROOT / ".workflow")` and then `import <stem>` by module name,
   plus doc links, CI config, and `MEMORY.md`/`WORKLOG.md`/`evals/` pointers. **Any hit means the
   artifact is load-bearing: do not move, rename, or delete it silently.** Stop, list the
   referring files with line numbers, and put the decision to the human — repoint the referrer,
   make the referrer resolve both the live and archived location, or leave the file where it is —
   then act on the answer in the same commit. A module imported by name that moves under
   `.workflow/archive/<date>/` fails at *collection*, so one archived script turns a green suite
   into a single collection error and takes every unrelated test down with it. The grep costs
   seconds; skipping it has already cost a full suite.

   **9b. Disposition by artifact class** — one rule per class, not one rule for the directory:

   | Class | What it is | Disposition |
   |---|---|---|
   | Transient scratch | The phase artifacts — `brainstorm.md`, `spec.md`, `plan.md` (including its `## Execution state` block — session scratch, not a durable doc), `patch_plan.md`, `review.md`, `learnings.md`, `realign.md` (and any `realign-stale-*.md`) — plus any working file whose only value was in-run | **Delete.** Their durable value already lives in the commits, the `evals/` cases, and wherever `memory.remember` routed it; and a stale `plan.md` left behind poisons the next run's re-ground, which trusts these files as truth. |
   | Evidence / receipts | Run receipts, verification JSON, live-harness output, deployment proofs, manifests — anything `MEMORY.md`, `WORKLOG.md`, a commit message, or an `evals/` case points at | **Archive with pointers still resolving.** Move to `.workflow/archive/<YYYY-MM-DD>/` and update every pointer 9a found, in the same commit. If a pointer cannot be updated, the file does not move. Never delete evidence a durable doc cites — a dangling pointer is worse than a kept file. |
   | Referenced code | Harnesses, importers, migration and one-off scripts that anything outside `.workflow/` imports, invokes, or path-inserts | **Never moved without updating the referrers first**, in the same commit — or left exactly where it is. Human decision per 9a; wrap never picks for them. |

   **9c. Recoverability decides how careful to be.** `.workflow/` is **tracked in some repos and
   gitignored in others** — check this one (`git check-ignore -v .workflow/`; `git ls-files
   .workflow/`) instead of assuming either way. Tracked: a delete is a committed change,
   recoverable from history, and belongs in this run's commit. Untracked/gitignored: a delete is
   **unrecoverable** — so archive rather than delete anything in the evidence class, and confirm
   with the human before deleting anything you had to classify by judgment rather than by the
   table.

   Commit the clean-up (where `.workflow/` is tracked) and push it, so the tree the next run
   re-grounds from is the tree that's archived.

**Why this order:** commits are local — nothing leaves the machine until push. Code → learnings
routed, evals deposited, and committed → **push** → clear the run. Clearing comes last precisely
because it is the only destructive step: everything worth keeping is already in git, in `evals/`,
or routed by `memory.remember` before a single file is touched. Never delete `learnings.md`
before `memory.remember` has actually routed every line — it
enforces this itself, but don't race ahead of it. Open a PR only if not committing straight to
`main`. When wrap-up is done, close with the ✅ done card from `SKILL.md`, not a next-phase card.
