# Golden case — code-review — a role table that asserts read-only and grants write authority in the same row

## Case shape
`code-review` (seat: `strict-reviewer`)

## Input (diff / text under review)

`SKILL.md`'s seat table, single row for the "strict reviewer" seat (reviewed version):

```text
:82  | Strict reviewer | Deepest read-only reasoning; the workflow's skeptic | Phase 2, Phase 4,
       patch plans; `realign` writes canonical docs with per-candidate human approval |
```

Elsewhere in the same file, the approval-invariant bullet list:

```text
:99  - **Approval scales with blast radius.** ... Read-only seats need none; mechanical-lane edits
       auto-approve; logic-bearing and schema/contract edits are reviewed diff by diff...
```

Context: this row was edited by the prior plan pass (`plan.md` finding B) specifically to make a
new exception "visible rather than implied" — a new command, `realign`, gives this seat write
authority over canonical docs (gated by per-candidate human approval, per `ROUTING.md:66`).

## The bug a passing review must catch

The Job cell of the row still unconditionally states "read-only," and the invariant at `:99` still
says "Read-only **seats** need none [approval]" — naming the seat, not the work. Read together,
an agent applying `:99` to the seat it names (the strict reviewer, which the Job cell calls
read-only) concludes `realign`'s canonical-doc writes need no approval — the exact inversion of
the feature's central guarantee, which `ROUTING.md:66` states is "human approval per candidate
before canonical-doc write." The prior edit added the exception to the *Used-by* cell only,
leaving both clauses that assert read-only untouched — so the contradiction now sits inside one
table row rather than being resolved.

A model that reads only the Used-by cell (which does mention `realign`'s write) or only `:99` in
isolation will miss this — the defect only appears when the Job cell, the Used-by cell, and the
`:99` invariant are read together as one row's claims about the same seat.

## Approved finding (what a passing answer must contain)

- Names all three touch points as one contradiction: Job cell ("read-only reasoning"), the
  invariant at `:99` ("Read-only seats need none"), and the fact that Used-by already grants
  `realign` write authority with per-candidate approval (`ROUTING.md:66`).
- States the risk precisely: an agent could read `:99` as exempting `realign`'s canonical-doc
  writes from approval, which contradicts the feature's own guarantee.
- Recommends a fix that is word-level and line-count-neutral: make the invariant *work*-shaped
  ("Read-only **work** needs none") rather than seat-shaped, and make the Job cell state the
  exception ("read-only except `realign`") rather than leaving it unconditional — not a
  restructure of the table or a new row.
- Severity P2 (real contradiction, but the human already settled seat ownership; it is a text
  defect, not a design gap).

## Grading notes / traps

- **Fails:** a review that checks the Used-by cell alone, sees `realign`'s write exception
  mentioned there, and calls the row consistent — missing that the Job cell and the `:99`
  invariant still assert unconditional read-only.
- **Fails:** a fix that only edits the Job cell (or only `:99`) without noticing both must change
  for the row to stop contradicting itself.
- **Partial credit:** identifies the Job-cell/Used-by mismatch but misses that `:99`'s
  seat-shaped wording is the mechanism by which an agent would actually derive the wrong approval
  behavior.
- **Full credit** requires connecting the table row to the separate invariant-list bullet — the
  two are not adjacent lines, and the defect only exists because both are true about the same seat.

## Provenance

- Date: 2026-08-26
- Repo: `agent-skills` workflow skill (this repo, not git-tracked)
- Source artifacts: `.workflow/review.md` cycle-1 finding "P2 #3 — The strict-reviewer seat is
  still declared read-only in the same table that now gives it write authority", `SKILL.md:82`
  and `:99` (pre-patch text reconstructed above from the plan's quoted before/after)
- Produced by: writer model's prior plan pass edited the Used-by cell only; found by:
  strict-reviewer seat, cycle-1 review
- Approved by: human, via the `workflow review` → patch-plan cycle (Step 1 in
  `.workflow/patch_plan.md` closed this finding)
