# Finish and report — `workflow wrap`

Compare the actual result with the user's objective and accepted scope. Reuse current verification
evidence; run missing checks or checks invalidated by subsequent changes. Fix in-scope regressions
rather than sending the user through another workflow cycle. If a consequential change needs
review, perform it or state the actual missing review requirement; an absent file alone is not proof
that no review happened.

Correct statements in affected docs that the work made stale. Preserve settled principles and
unrelated work; an unresolved conflict is a product decision, not authority to rewrite direction.
Update relevant TODO/roadmap entries honestly. Capture a durable learning only when it adds
non-obvious knowledge; see [learning](learning-worklog.md). No mandatory global documentation sweep.

Commit or publish only within existing authorization. Group coherent changes, and verify that the
committed/released result is the one checked. Do not imply that `wrap` authorizes deployment,
spending, pushing, or publication by itself.

Clear the completed run's `.workflow/` scratch as part of closeout, after preserving its value:
- Put operational scripts in the repo's source/scripts/SQL locations and durable lessons in their
  proper home. Keep required receipts, decisions, and historical evidence in the existing evidence
  location, or `docs/archive/<run>/` if there is no convention. Do not archive under `.workflow/`.
- Find external references by path, filename, and module stem; update memory, docs, imports,
  invocations, and tests in the same change. Preserve immutable evidence bytes and use a durable
  relocation record when needed. Preserve access/ignore restrictions; moving private scratch
  does not authorize publishing it.
- Verify affected consumers and that needed evidence is recoverable, then remove disposable
  scratch. A live dependency must be resolved before its target is removed. Never flush another
  active task's files or blindly delete legacy material belonging to unrelated runs.

An incomplete milestone keeps its active tracker and required scratch until resumed or explicitly
parked with a durable handoff. If preservation or reference repair is blocked, retain the affected
files and report cleanup pending; do not claim closeout is complete.

Report the outcome and evidence concisely. Distinguish local verification, deployment, and user
access when relevant; name unresolved dependencies and the next concrete action. Complete the
objective only when its stated finish line has actually been reached.
