# Phase 2 — Audit & Plan — `workflow plan`

> ⚠️ **Invoke the `workflow` skill before acting on this file** — reading it raw is how the closing next-step card gets dropped.

Seat: **strict reviewer** — mapping in `ROUTING.md`. Rationale: this is the deepest-reasoning
read-only work, and the auditor should be a *different vendor* from the spec's author —
cross-vendor scrutiny catches hallucinated signatures and blind spots the generator's own
family shares. A harness parallel-breadth mode is permitted here per `SKILL.md`'s
read-only-breadth invariant.

Four things the plan depends on and a fresh reader won't infer:

- **Write the plan as you draft it.** Open `.workflow/plan.md` with `Status: drafting` before
  drafting anything, then append each step as it is settled — an audit held in context is one
  context death from gone, and resetting mid-audit should cost the re-read, not the work.
  `## TODO impacts` and `## Product doc impacts` are written last; they and a finished checklist flip `Status` to
  `complete`. Until then Phase 3 must refuse the plan: a half-drafted checklist looks exactly like
  a finished one.

- **Step shape.** Every checklist step carries a verification check and a `Skills:` line:

  ```markdown
  - [ ] Step N — <what> (<files>)
    - Check: <verification>
    - Skills: <path/to/SKILL.md>[, <path/to/SKILL.md>] | none
  ```

- **Why paths, not names.** Phase 3 has to *open* those files before touching the code, and
  name-only skill lookup is not portable across harnesses. A bare name is a step Phase 3 cannot
  act on; a resolved path is what makes execution pick up the right repo conventions.
- **`## TODO impacts` and `## Product doc impacts` are wrap's input.** Wrap reads those lists to
  update `TODO.md` and the product docs, which is the whole mechanism keeping them synced to the
  codebase instead of drifting. A plan that omits a section leaves that doc stale. The audit is
  already reading `PRODUCT.md`/`DESIGN.md` to flag conflicts — this is where that flag becomes
  durable instead of evaporating with the session.

Run it directly, or hand it to a fresh session on the strict-reviewer seat by pasting:

```text
Read .workflow/spec.md, the repo, and PRODUCT.md/DESIGN.md if this touches product or UI (flag anything that conflicts with either). Find architectural blind spots, circular dependencies, and hallucinated signatures — confirm against the real code. Then rewrite it as a sequential, file-by-file checklist, core interfaces before consumers, each step with a verification check. Lead the checklist with whatever's most likely to need a human tweak (data model/schema shape, type interfaces, user-facing behavior) — put mechanical/rote steps at the bottom. Also, before writing the checklist: enumerate the available custom skills (repo skill directories + installed skills, frontmatter name/description only — the same inventory memory.remember takes) and give every step a "Skills:" line recording the resolved SKILL.md path of each skill that genuinely matches that step's work, or "Skills: none" — match on the step's task shape rather than keywords, don't pad, and expect most steps to genuinely need none; if two skills overlap, name the more specific one. Then cross-check TODO.md at the repo root (if present): propose (don't auto-include) any cheap adjacent TODO item touching the same files as an optional step, and list every TODO item this plan would complete, partially complete, obsolete, or conflict with under "## TODO impacts" at the bottom of the plan, as item name → expected effect. Then do the same for the product docs that exist — PRODUCT.md, DESIGN.md, ROADMAP.md — under "## Product doc impacts": for each, either "no statement changes" or the specific statements this plan would make untrue and what should replace them. Three kinds matter and are the ones that get missed: a statement of current state, scope or stack this makes stale; an open decision this work resolves; and a settled principle or boundary this work contradicts — mark that third kind ESCALATE, because changing it is a product decision for the human, not a doc edit. Write .workflow/plan.md BEFORE you start drafting, with a five-line provenance header — Command, Created (date), Base (current git sha), Inputs (.workflow/spec.md @ its own Base sha), Status (drafting) — and append each step to the file as you settle it rather than holding the checklist in context and saving at the end. Write "## TODO impacts" and "## Product doc impacts" last, then set Status to complete. If you are resuming a drafting plan.md, continue after the last step already written rather than starting over.
```

**Close with the next-step card** (format in `SKILL.md`) — mandatory, no substitute. A conversational closer ("want me to proceed?") is not the card; if in doubt, print it.
