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

Keep evidence and receipts. Do not automatically clear `.workflow/`. Before an authorized cleanup,
check for imports, invocations, and links to candidates (including module stems), and preserve
recoverability. Repoint consumers in the same change or leave their targets in place. A gitignored
artifact may have no recoverable history; do not delete it based on a filename alone.

Report the outcome and evidence concisely. Distinguish local verification, deployment, and user
access when relevant; name unresolved dependencies and the next concrete action. Complete the
objective only when its stated finish line has actually been reached.
