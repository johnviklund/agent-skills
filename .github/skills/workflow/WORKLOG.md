# Worklog

A bounded, rolling, git-pointing index of what was built or changed in this repo, newest first.
Not a source of truth and not an archive — canonical docs and the (absent, here) git history own
that; entries only point at commits. Cap: keep roughly the 15 most recent entries; delete the
oldest when appending would exceed that.

## 2026-08-26 · `workflow realign` command specified, planned, executed, reviewed · Copilot CLI · Claude Sonnet 5
- Added `references/realign.md` (new `workflow realign` command: canonical-doc drift check against
  code, per-candidate human-approved rewrite) and wired it into `SKILL.md` (seat table, command
  index) and `ROUTING.md` (model/effort/approval mapping).
- Patch cycle 1 closed 4 findings: unblocked `realign`'s own resume path (P1, clean-worktree gate
  scoped outside `.workflow/`), separated the retained-stale artifact from the fresh-run path (P2,
  `realign-stale-<date>.md`), fixed a seat-table row that both asserted read-only and granted write
  authority (P2), and plained the `TODO.md` read-only wording (P3).
- Commits: 0008a14
- Review: ship as-is (cycle 2, clean — 0 P0/P1/P2, 3 P3s deferred) @ 0008a14
- Why: give the workflow a repeatable, reviewed way to catch canonical-doc drift instead of relying
  on ad hoc memory.remember passes to notice it.
