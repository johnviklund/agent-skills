# Check product direction — `workflow realign`

Compare existing PRODUCT.md and, where relevant, DESIGN.md with shipped evidence. If neither
exists, explain the missing basis; do not manufacture product direction. This command does not
implement code or advance another task's status.

Choose a relevant evidence window from doc history and changes since, then confirm claims against
current behavior. Preserve unrelated or in-flight edits; their existence does not prevent reading
and proposing. Distinguish deployed behavior from local implementation and intended direction.

For each material mismatch, present the current statement, proposed exact change, evidence, and
reason. Correctness of current-state prose and a change to settled direction are different decisions.
Obtain human acceptance for directional changes unless that exact change is already authorized.
Do not turn code drift into permission to change the product's principles. Group related wording
into one meaningful decision, rather than requiring approval for each sentence.

Use a focused [direction check](direction-stress-tests.md) only if an unresolved tension warrants it.
Apply authorized changes after confirming the underlying text has not changed; otherwise reconcile
the overlap first. Keep a concise decision/evidence record when the work spans sessions, preserving
previous evidence instead of blindly overwriting a fixed filename. Report accepted changes and
remaining decisions. No mandatory stress-test count, clean-tree gate, or full workflow restart.
