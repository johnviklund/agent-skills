# Phase 1 — Spec — `workflow spec`

> ⚠️ **Invoke the `workflow` skill — do not just read this file.** If you reached this reference without invoking the `workflow` skill this turn, stop and invoke it first. These reference files are the skill's controlling contract (seat, verification bar, and the mandatory closing next-step card); reading them raw skips that contract — which is how required plan/execute steps get silently dropped.


Seat: **default executor** — mapping in `ROUTING.md`. Rationale: scoped,
verify-against-the-code work — the value lane at high effort.

Read `AGENTS.md` + `MEMORY.md` + `PRODUCT.md`/`DESIGN.md` (if relevant) +
`.workflow/brainstorm.md` (if present) + the code. Propose the architecture, modified interfaces,
and every impacted file. Verify each existing signature against the actual code and flag anything
only inferred. Save to `.workflow/spec.md`.

If handing this to a fresh session on the default-executor seat, paste:

```text
Read AGENTS.md + MEMORY.md + PRODUCT.md/DESIGN.md (if this touches product direction or UI) + .workflow/brainstorm.md (if present) + the code. We're refactoring [feature]. Propose the architecture, the modified interfaces, and every impacted file. Verify each existing signature against the actual code and flag anything you're only inferring. Save to .workflow/spec.md.
```

Close with the next-step card (format in `SKILL.md`).
