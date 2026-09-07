# Specify a consequential decision — `workflow spec`

Use the user's request, existing brief if present, and relevant code. No brainstorm artifact is
required. Write `.workflow/spec.md` or the requested output path, focused on the behavior or
contract that needs agreement: its meaning, affected boundaries, failure states, and acceptance
checks. Verify existing interfaces against code and label unresolved assumptions.

Preserve settled direction. Describe implementation detail only where it constrains correctness
or coordinates consumers; avoid an exhaustive file inventory. Identify the concrete decision
and its consequence if human input is needed, while completing independent sections.

Stop when the implementer has enough information to act. Report the result and any unresolved
decision. Do not mandate an audit-and-plan phase merely because a spec now exists.
