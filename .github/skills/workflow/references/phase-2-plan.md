# Plan delivery — `workflow plan`

Plan from the task, supplied artifact, and current code; a separate spec is optional. Inspect
interfaces and dependencies that can invalidate the approach. Surface a material conflict with
settled product direction; resolve routine engineering choices directly.

Write a short plan of demonstrable outcomes, not a sequential inventory of files. Usually a few
coherent milestones suffice; this is guidance, not a quota. For each, state what becomes usable
and the meaningful check that proves it. Detail the next slice; expand later milestones only
when the next implementation decision needs it. Group producer/consumer changes coherently.

Record objective, scope, material decisions, and any actual delivery blockers alongside the
milestones. Resolve access, runtime, data, and authorization dependencies early enough that they
do not emerge only after all code is complete. Preserve the full requested outcome across slices.

Reuse a supplied plan without erasing completed work. Separate preparation checks from deployed
behavior and stakeholder access. Link detailed contracts or evidence instead of duplicating them.
No mandatory per-step skill paths, TODO-impact inventory, or product-doc inventory; note specific
doc changes only where the planned behavior will make a statement stale.

This command produces a plan. End with the first executable action and `workflow execute` (or
the concrete unresolved decision), without demanding a reset, model swap, or extra audit phase.
