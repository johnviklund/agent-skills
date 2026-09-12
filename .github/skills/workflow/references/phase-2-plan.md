# Phase 2 — Audit & Plan — `workflow plan`

> ⚠️ **Invoke the `workflow` skill before acting on this file** — reading it raw is how the closing next-step card gets dropped.

Seat: **strict reviewer** — mapping in `ROUTING.md`. Rationale: this is the deepest-reasoning
read-only work, and the auditor should be a *different vendor* from whoever wrote the spec or
brainstorm — cross-vendor scrutiny catches hallucinated signatures and blind spots the generator's
own family shares. A CLI's parallel-breadth mode is permitted here per `SKILL.md`'s
read-only-breadth invariant.

Input is always `.workflow/<slug>/brainstorm.md` — the requirements of record — plus
`.workflow/<slug>/spec.md` when it exists. Without a spec the audit also does the spec's job:
verify every interface, signature and column it will touch against the real code before writing
a step. A spec never replaces the brainstorm: its scope and non-goals are checked for coverage
regardless, because a constraint dropped during compression would otherwise vanish from every
downstream check. Either way the plan is the same shape.

Five things the plan depends on and a fresh reader won't infer:

- **The plan is a checklist the executor follows, not a report the human reads.** The audit's
  thinking is unbounded; what lands in `plan.md` is what Phase 3 needs to act and what wrap needs to
  reconcile. Findings are a **one-line-per-row table**, not paragraphs; a step never restates a
  finding's rationale — it cites the row (`(F3)`) and moves on. Corrections to the input (a wrong
  hash, a stale signature) are recorded as a finding row and *used* in the steps; the input
  artifact is not edited and no step is spent "correcting the spec". Budget: ≤ ~100 lines, ≤ 12 steps.

- **An undecided input stops the audit; it never becomes Step 1.** If the audit finds a question
  only the human can answer (which fixtures, which of two sources, whether a coverage rule is
  relaxed), the plan is not written until it is answered: stop, ask it in the *Reporting* shape
  from `SKILL.md` (lettered options, recommended default), wait, then plan. A checklist step that
  says "settle X with the user" is an interview disguised as a plan.

- **A step is one verifiable unit, not one file.** Group by what one check proves — a module and
  its tests, a contract and its consumer — and let a step span files. If honest steps exceed 12 the
  run is too big: propose where to split into two runs and ask, rather than writing a longer plan.
  Lead with whatever is most likely to need a human tweak (data model, type interfaces,
  user-facing behavior); mechanical steps last.

- **Step shape — four lines, nothing else.** Every step carries a verification check and a
  `Skills:` line with resolved paths, because Phase 3 has to *open* those files and name-only
  lookup is not portable across CLIs:

  ```markdown
  - [ ] Step N — <what, in one line> (<files>) (F<n> if a finding applies)
    - Check: <the command or assertion that proves it>
    - Skills: <path/to/SKILL.md>[, <path/to/SKILL.md>] | none
  ```

- **`## Coverage` proves nothing was lost.** One line per brainstorm scope item → the step(s)
  that deliver it, and one per non-goal → `untouched`. A scope item with no step is a gap to
  fix before the plan is complete (or an explicit `dropped: <why>` the human confirms); review
  re-checks the same list against the diff.

- **`## Risks` names where the human's attention goes.** Three lines at most: the riskiest step
  and why, what this change could break outside its own files, and the one option considered and
  not taken. This is not a findings echo — it is the answer to "where should I actually look".

- **`## TODO impacts` and `## Product doc impacts` are wrap's input**, which keeps `TODO.md` and
  the product docs synced to the code instead of drifting. They are lists of *changes*: a doc or
  item this plan leaves untouched gets one line ("`PRODUCT.md` — no changes"), never an argument
  for why. A settled principle or boundary the plan would contradict is marked **ESCALATE** —
  changing it is the human's product decision, not a doc edit. A cheap adjacent TODO item touching
  the same files is *mentioned* here in one line as optional, never added as a step.

Run it directly, or hand it to a fresh session on the strict-reviewer seat by pasting:

```text
Read .workflow/<slug>/brainstorm.md — the requirements of record — and .workflow/<slug>/spec.md if it exists (<slug> is the run named in my command; every file below lives in that folder); read the repo; read PRODUCT.md/DESIGN.md if this touches product or UI. Audit the input against the real code: hallucinated signatures, wrong columns or hashes, circular dependencies, architectural blind spots, and anything the input silently assumes — confirm each against the code, not the input. If you hit a question only I can answer, stop before writing any step and ask it: one decision per question, plain words, the options on their own lines with a recommended default marked; then wait. Otherwise write .workflow/<slug>/plan.md — BEFORE drafting, open it with a five-line provenance header (Command, Created (date), Base (current git sha), Inputs (the input artifact @ its own Base sha), Status (drafting)) and append as you go rather than holding the plan in context. The file has exactly six sections. "## Findings": a table, one row per finding — # · what is true · what it changes — no paragraphs; corrections to the input are rows here and the correct value is simply used downstream, never a step to edit the input. "## Checklist": at most 12 steps, each one verifiable unit (may span files), core interfaces before consumers, the steps most likely to need my tweak first and mechanical ones last; each step is exactly: the [ ] line (what, files, finding refs), "- Check:" (the command or assertion that proves it), and "- Skills:" (resolved SKILL.md paths that genuinely match the step's task shape, or none — enumerate repo skill directories + installed skills by frontmatter first; most steps need none). No rationale under steps. If honest steps exceed 12, write no checklist — propose where to split into two runs and ask. "## Coverage": one line per brainstorm scope item → the step numbers that deliver it (a scope item with no step is a gap — fix the checklist, or write "dropped: <why>" and ask me), and one line per non-goal → "untouched". "## Risks": at most three lines — the riskiest step and why, what this could break outside its own files, the one option considered and not taken. "## TODO impacts": for TODO.md (if present), each item this plan completes, partially completes, obsoletes, or conflicts with, as item → effect; one line "none" if nothing; a cheap adjacent item touching the same files may be mentioned in one line as optional, never added as a step. "## Product doc impacts": for each of PRODUCT.md, DESIGN.md, ROADMAP.md that exists, either "<doc> — no changes" in one line, or the specific statements this plan makes untrue and what replaces them — a stale statement of state/scope/stack, an open decision this resolves, or a settled principle this contradicts, which is marked ESCALATE. Write "## Coverage", "## Risks" and the two impact sections last, then set Status to complete. The whole file stays under ~100 lines; cut prose, never checks. If resuming a drafting plan.md, continue after the last step written. In chat report only: findings count with any that change scope, step count, ESCALATE items, and decisions you need from me — then the next-step card.
```

**Close with the next-step card** (format in `SKILL.md`) — mandatory, no substitute. Read `ROUTING.md` now and fill row 2 with the next seat's concrete vendor · model · effort · context window and first fallback; a seat name or "see ROUTING.md" is a defect. A conversational closer ("want me to proceed?") is not the card; if in doubt, print it.
