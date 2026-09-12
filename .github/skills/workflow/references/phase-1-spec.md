# Phase 1 — Spec (optional) — `workflow spec`

> ⚠️ **Invoke the `workflow` skill before acting on this file** — reading it raw is how the closing next-step card gets dropped.

Seat: **default executor** — mapping in `ROUTING.md`. Rationale: scoped, verify-against-the-code
work — the value lane at high effort.

**This phase is optional.** Phase 2 audits against the real code either way, so a spec earns its
cost only when a second vendor's independent read of the codebase is worth a phase: schema/SQL/
contract-coupled work, subsystems the brainstorm could not size, or an unfamiliar codebase. The
brainstorm's closing card says which; if `workflow spec` is invoked when the card said `plan`, ask
before running it. When it does run, it is a *map for the audit*, not a design document.

Two things the spec depends on and a fresh reader won't infer:

- **Nothing written here is settled.** The spec is a hypothesis that Phase 2 audits against the
  real repo, which is why an inferred signature has to be *marked* as inferred rather than
  smoothed into confident prose — a flagged guess gets checked, a confident wrong one survives the
  audit and reaches execution.
- **Write the spec as you draft it.** Open `.workflow/<slug>/spec.md` with `Status: drafting` before
  drafting anything, then append each section as it settles. The impacted-files table is written
  last; it flips `Status` to `complete`.

**Shape (≤ ~60 lines):** `## Approach` — the architecture in ≤10 lines, no rationale beyond one
clause per decision; `## Interfaces` — signatures, columns and contract versions as code lines, each
tagged `verified` or `inferred`; `## Impacted files` — a table of path → change in one phrase.
No prose sections beyond these three.

Run it directly, or hand it to a fresh session on the default-executor seat by pasting:

```text
Read AGENTS.md + MEMORY.md + PRODUCT.md/DESIGN.md (if this touches product direction or UI) + .workflow/<slug>/brainstorm.md (<slug> is the run named in my command; every file below lives in that folder) + the code. We're building [feature]. Write a spec that is a map for an audit, not a design document, in three sections and at most ~60 lines: "## Approach" (the architecture in ≤10 lines, one clause of rationale per decision), "## Interfaces" (every new or modified signature, column and contract version as a code line, each tagged verified — you checked it against the actual code — or inferred), "## Impacted files" (a table, path → change in one phrase). Write .workflow/<slug>/spec.md BEFORE you start drafting, with a five-line provenance header — Command, Created (date), Base (current git sha), Inputs (.workflow/<slug>/brainstorm.md @ its own Base sha), Status (drafting) — and append each section to the file as it settles rather than holding it in context. Write the impacted-files table last, then set Status to complete. If you are resuming a drafting spec.md, continue after the last section already written. In chat, report only: sections written, the count of inferred interfaces, and anything you could not verify.
```

**Close with the next-step card** (format in `SKILL.md`) — mandatory, no substitute. Read `ROUTING.md` now and fill row 2 with the next seat's concrete vendor · model · effort · context window and first fallback; a seat name or "see ROUTING.md" is a defect. A conversational closer ("want me to proceed?") is not the card; if in doubt, print it.
