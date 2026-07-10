# Phase 3 — Execute — `workflow execute`

Model/effort/CLI: per the routing tables in `SKILL.md` (three Phase 3 rows — pick per *step
shape*, not one tier for the whole phase): mechanical → Luna row, logic-bearing → Terra row,
schema/SQL/contract-coupled → Sol row. Rationale: long-horizon terminal execution is GPT-5.6's
edge over both Claude models. Never `ultra` here — execution writes.

If already in the right tool: read `.workflow/plan.md`, capture the baseline test state (which
failures are pre-existing/environmental), then work ONE step at a time — edit, run checks, show
the diff, commit. If an edge case forces a deviation, take the conservative option, note it under
a "Deviations" section in `.workflow/plan.md`, and keep going — don't silently improvise. Stop on
any failed check or unresolved file. Keep contract versions in lockstep; no compat shims. Append
this phase's learnings as they happen (see `references/learning-worklog.md`) rather than only at
the end. When `/context` shows the window filling mid-plan, run `workflow compact` (see
`references/compact.md`) between steps — never mid-step — rather than letting the CLI
auto-compact.

If handing this to a fresh session, paste:

```text
Read .workflow/plan.md. First capture the baseline test state (which failures are pre-existing or environmental). Then work ONE step at a time: edit, run checks, show the diff, commit. If an edge case forces a deviation from the plan, take the conservative option, note it under a "Deviations" section in .workflow/plan.md, and keep going — don't silently improvise. Stop on any failed check or unresolved file. Keep contract versions in lockstep; no compat shims. Use the plan as a mutable tracker.
```

Codex-only optional autonomous loop: `/goal` can drive the whole plan end to end without
re-prompting each step — worth knowing about, but not the default here; only reach for it if
explicitly asked, and keep `review each diff` on SQL/contract steps even under a goal.

Close with the next-step card (format in `SKILL.md`).
