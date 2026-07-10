# Phase 2 — Audit & Plan — `workflow plan`

Model/effort/CLI: per the routing tables in `SKILL.md` (Phase 2 row). Rationale for the seat:
this is the deepest-reasoning read-only work, and the auditor should be a *different vendor*
from the spec's author — cross-vendor scrutiny catches hallucinated signatures and blind spots
the generator's own family shares. In Codex, `ultra` is permitted for a genuinely wide audit
(many files/subsystems verifiable independently) per the read-only exception in `SKILL.md`.

Read `.workflow/spec.md`, the repo, and `PRODUCT.md`/`DESIGN.md` if relevant (flag conflicts).
Find architectural blind spots, circular dependencies, and hallucinated signatures — confirm
against the real code. Rewrite as a sequential, file-by-file checklist, core interfaces before
consumers, each step with a verification check. Lead with whatever's most likely to need a human
tweak (data model/schema shape, type interfaces, user-facing behavior); put mechanical steps at
the bottom. Save to `.workflow/plan.md`.

If handing this to a fresh Copilot session, paste:

```text
Read .workflow/spec.md, the repo, and PRODUCT.md/DESIGN.md if this touches product or UI (flag anything that conflicts with either). Find architectural blind spots, circular dependencies, and hallucinated signatures — confirm against the real code. Then rewrite it as a sequential, file-by-file checklist, core interfaces before consumers, each step with a verification check. Lead the checklist with whatever's most likely to need a human tweak (data model/schema shape, type interfaces, user-facing behavior) — put mechanical/rote steps at the bottom. Save to .workflow/plan.md.
```

Close with the next-step card (format in `SKILL.md`).
