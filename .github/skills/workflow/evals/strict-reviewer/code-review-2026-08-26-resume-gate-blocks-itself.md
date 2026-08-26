# Golden case — code-review — a precondition gate that blocks its own documented resume path

## Case shape
`code-review` (seat: `strict-reviewer`)

## Input (diff / text under review)

`references/realign.md` (a new `workflow realign` command spec) contained these two passages,
~10 lines apart, both present in the same reviewed version:

```text
:15  ...checked "before creating or resuming the run artifact":
:17  - The current directory is a Git repository and its tracked worktree is clean. If not,
       refuse, name the condition or changed paths, and ask the human to run the command in the
       repository or finish the tracked changes first.
```

```text
:25  - An existing `.workflow/realign.md` with `Status: drafting` is resumable only when its
       recorded base is still an ancestor of `HEAD` and none of its in-scope canonical docs
       changed after the artifact was created. Otherwise set its status to `stale`, retain it as
       evidence, tell the human what invalidated it, and begin a fresh run.
:36  - Create `.workflow/realign.md` before mining evidence.
```

Supporting context available to the reviewer: `references/wrap.md:113` states `.workflow/` is
"tracked in some repos and gitignored in others"; `references/bootstrap.md:50-53` treats tracking
it as a first-class choice; `spec.md:29-30` (this command's own spec) argues the gate should
"refuse rather than trying to distinguish the command's edits from uncommitted work."

## The bug a passing review must catch

In a repo that tracks `.workflow/`, the artifact `:25-28` describes resuming (a `Status: drafting`
`.workflow/realign.md`) is itself a tracked, uncommitted modification — exactly what the `:17` gate
refuses on. So precondition 1 (clean tracked worktree) fails on precisely the state the command
documents as resumable, and the resume path can never be reached. This also blocks a fresh first
run: the gate demands cleanliness, then the command immediately dirties the tracked tree by
creating the artifact, with no rule saying whether that artifact is later committed or excluded.

A model that only diffs line-by-line or checks each passage for internal consistency will pass
this: both `:17` and `:25-28` are individually well-formed. Catching it requires connecting two
requirements ~10 lines apart as *the same command's* contract and simulating the state each one
implies.

## Approved finding (what a passing answer must contain)

- Names the contradiction: the clean-worktree gate (`:17`) and the resume path (`:25-28`) are
  mutually exclusive in any repo tracking `.workflow/`, because that directory is the command's
  own edit area.
- Identifies severity as P1 (not P0 — no data loss/crash, but a command that refuses forever on
  its own documented state) and gives a fix shaped as "scope the gate to tracked changes *outside*
  `.workflow/`, plus one clause on whether the run artifact is committed or excluded" — not merely
  "add an exception somewhere."
- Optionally notes the fresh-run corollary: the gate also blocks step 1 of a first run, since
  creating the artifact immediately dirties the tree the gate just required clean.

## Grading notes / traps

- **Fails:** a review that flags `:17` or `:25-28` as fine in isolation, or that treats this as a
  documentation nit rather than an unreachable-path defect.
- **Fails:** a fix suggestion that removes the cleanliness gate entirely rather than scoping it —
  that reintroduces exactly the ambiguity the gate exists to prevent (per `spec.md:29-30`'s own
  rationale).
- **Partial credit:** catches the contradiction but misses that it also blocks the *first* run,
  not only resume.
- **Full credit requires citing both line ranges** (or their paraphrase) as the same defect, not
  two separate findings.

## Provenance

- Date: 2026-08-26
- Repo: `agent-skills` workflow skill (this repo, not git-tracked)
- Source artifacts: `.workflow/review.md` cycle-1 finding "P1", `references/realign.md` (pre-patch
  text reconstructed above from the plan's quoted before/after)
- Produced by: writer model drafted `realign.md`; found by: strict-reviewer seat, cycle-1 review
- Approved by: human, via the `workflow review` → patch-plan cycle (Step 2 in
  `.workflow/patch_plan.md` closed this finding)
