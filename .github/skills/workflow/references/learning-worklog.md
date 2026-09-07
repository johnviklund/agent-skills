# Learn and log — `workflow learn` / `workflow log`

Capture lessons that a future session could not readily infer from the code or existing docs.
Save a verified lesson when established, not only at wrap or on an explicit `learn` command.
If its durable destination is not yet appropriate, keep a short pending-learning note in the
active plan and resolve it at closeout. Mark unverified observations provisional. Use the final
verified behavior and later decisions, not a superseded intermediate assumption.
Put each lesson in its existing home: environment facts in memory, reusable task guidance in a
skill, product/design decisions in their canonical document. Do not turn one incident into a
universal requirement or require another skill invocation to write a straightforward entry.

## Make each run improve future work

At closeout, inspect material failures, reviewer findings, and expensive detours from this run.
For each verified lesson, identify the cause and what would prevent recurrence, then update the
smallest appropriate existing test, skill, operating rule, or reference. A log entry alone is not
prevention. Add a regression test when it meaningfully exercises a recurring failure; avoid tests
that merely repeat implementation wording. Keep provisional theories out of standing rules.
Verify that any destination cited actually contains the lesson and can be found by a future task.
Report what changed and where, or explicitly say no new durable lesson was warranted. Do not
manufacture a lesson, skill, or evaluation case to satisfy a quota.

## Keep active memory bounded

`MEMORY.md` is a small active working set, not an append-only history. Before writing and at
closeout, measure it against the repo's existing size budget; when none exists, use roughly
1,500 words as a default target. Consolidate before adding: replace superseded current state,
merge duplicates, and move historical detail to `MEMORY_ARCHIVE.md` or the repo's durable
solution notes, searched only when relevant. Route product/design doctrine and reusable how-to
knowledge to their proper owners instead of copying it into active memory.

Keep current safety boundaries and unresolved blockers visible. Before replacing text with a
short pointer, verify the destination contains the actual information. Preserve provenance and
recoverability; never truncate mechanically to hit the budget. If useful active content still
cannot fit, report the specific unresolved consolidation and recommend the available memory
cleanup command. Do not silently append beyond the budget or claim memory maintenance complete.
Record size before/after when memory changed; do not load the entire archive at startup.

Durable entries must cite durable files or verified commits, never depend on `.workflow/` files.
If a lesson needs a scratch artifact as evidence, preserve that artifact in its durable location
before writing the citation. The active plan can hold the pending lesson until that is done.

`workflow log` adds a concise dated entry to `WORKLOG.md`: outcome, reason, and verified commit
or evidence link. State explicitly if the work is uncommitted or not deployed. Keep roughly 15
recent entries where older entries are recoverable; avoid duplicating the execution transcript.

Maintain evaluation cases only when a confirmed failure or difficult decision discriminates
between good and bad behavior. Existing directories remain compatible: `spec` → `default-executor`,
`plan-audit` and `code-review` → `strict-reviewer`, `mechanical-transform` → `mechanical-lane`.
A case includes the minimum self-contained input, expected behavior, failure criteria, and
provenance. Approved prose is not automatically a good golden answer. Keep about 15 useful cases
per shape, replacing weaker cases only when recoverable. No case deposit is required for routine
work. Historical cases do not make historical phase rules mandatory.

Follow the hub's [model-only attribution](../SKILL.md#model-only-attribution) rule for every saved
entry. Preserve technical provenance such as commands, versions, dates, and revisions.
