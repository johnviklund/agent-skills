# Learn and log — `workflow learn` / `workflow log`

Capture lessons that a future session could not readily infer from the code or existing docs.
Save a verified lesson when established, not only at wrap or on an explicit `learn` command.
If its durable destination is not yet appropriate, keep a short pending-learning note in the
active plan and resolve it at closeout. Mark unverified observations provisional. Use the final
verified behavior and later decisions, not a superseded intermediate assumption.
Put each lesson in its existing home: environment facts in memory, reusable task guidance in a
skill, product/design decisions in their canonical document. Do not turn one incident into a
universal requirement or require another skill invocation to write a straightforward entry.

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
