# Phase 1 — Spec — `workflow spec`

> ⚠️ **Invoke the `workflow` skill before acting on this file** — reading it raw is how the closing next-step card gets dropped.

Seat: **default executor** — mapping in `ROUTING.md`. Rationale: scoped,
verify-against-the-code work — the value lane at high effort.

Two things the spec depends on and a fresh reader won't infer:

- **Nothing written here is settled.** The spec is a hypothesis that Phase 2 audits against the
  real repo, which is why an inferred signature has to be *marked* as inferred rather than
  smoothed into confident prose — a flagged guess gets checked, a confident wrong one survives the
  audit and reaches execution.
- **Write the spec as you draft it.** Open `.workflow/spec.md` with `Status: drafting` before
  drafting anything, then append each section as it settles. Verifying signatures against an
  unfamiliar codebase is exactly the long read that runs a session out of context, and a spec held
  in context is one context death from gone. The impacted-files list is written last; it flips
  `Status` to `complete`.

Run it directly, or hand it to a fresh session on the default-executor seat by pasting:

```text
Read AGENTS.md + MEMORY.md + PRODUCT.md/DESIGN.md (if this touches product direction or UI) + .workflow/brainstorm.md (if present) + the code. We're refactoring [feature]. Propose the architecture, the modified interfaces, and every impacted file. Verify each existing signature against the actual code and flag anything you're only inferring. Write .workflow/spec.md BEFORE you start drafting, with a five-line provenance header — Command, Created (date), Base (current git sha), Inputs (.workflow/brainstorm.md @ its own Base sha, or none if there wasn't one), Status (drafting) — and append each section to the file as it settles rather than holding the spec in context and saving at the end. Write the impacted-files list last, then set Status to complete. If you are resuming a drafting spec.md, continue after the last section already written rather than starting over.
```

**Close with the next-step card** (format in `SKILL.md`) — mandatory, no substitute. A conversational closer ("want me to proceed?") is not the card; if in doubt, print it.
