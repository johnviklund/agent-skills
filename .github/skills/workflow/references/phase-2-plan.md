# Phase 2 — Audit & Plan — `workflow plan`

> ⚠️ **Invoke the `workflow` skill before acting on this file** — reading it raw is how the closing next-step card gets dropped.

Seat: **strict reviewer** — mapping in `ROUTING.md`. Rationale: this is the deepest-reasoning
read-only work, and the auditor should be a *different vendor* from the spec's author —
cross-vendor scrutiny catches hallucinated signatures and blind spots the generator's own
family shares. A harness parallel-breadth mode is permitted here per `SKILL.md`'s
read-only-breadth invariant.

Read `.workflow/spec.md`, the repo, and `PRODUCT.md`/`DESIGN.md` if relevant (flag conflicts).
Find architectural blind spots, circular dependencies, and hallucinated signatures — confirm
against the real code. Rewrite as a sequential, file-by-file checklist, core interfaces before
consumers, each step with a verification check. Lead with whatever's most likely to need a human
tweak (data model/schema shape, type interfaces, user-facing behavior); put mechanical steps at
the bottom. Save to `.workflow/plan.md`.

**Suggested skills per step.** Before writing the checklist, enumerate the available custom
skills — repo-local skill directories plus installed/plugin skills — reading only each skill's
frontmatter (name + description), the same inventory `memory.remember` does. Then give every
step a `Skills:` line: the skill(s) whose description genuinely matches that step's work, or
`Skills: none`. Match on the step's task shape, not keywords; most steps legitimately need none —
never pad. If two skills overlap, name the more specific one. Step shape in the plan:

```markdown
- [ ] Step N — <what> (<files>)
  - Check: <verification>
  - Skills: <skill-name>[, <skill-name>] | none
```

This is what makes execution pick up the right repo conventions: Phase 3 reads the `Skills:`
line per step and loads those skills before touching the files.

**TODO impact check.** After drafting the checklist, cross-check repo-root `TODO.md` (if
present): (a) a cheap adjacent TODO item touching the *same files* as a plan step may be worth
folding in — propose it as an explicit optional step and ask, never auto-include; (b) flag every
TODO item this plan would complete, partially complete, make obsolete, or conflict with, and
list them at the bottom of the plan under `## TODO impacts` (item name → expected effect). Wrap
uses that list to update `TODO.md`, which is what keeps the scratchpad synced to the codebase
instead of drifting.

If handing this to a fresh session on the strict-reviewer seat, paste:

```text
Read .workflow/spec.md, the repo, and PRODUCT.md/DESIGN.md if this touches product or UI (flag anything that conflicts with either). Find architectural blind spots, circular dependencies, and hallucinated signatures — confirm against the real code. Then rewrite it as a sequential, file-by-file checklist, core interfaces before consumers, each step with a verification check. Lead the checklist with whatever's most likely to need a human tweak (data model/schema shape, type interfaces, user-facing behavior) — put mechanical/rote steps at the bottom. Also: enumerate the available custom skills (repo skill directories + installed skills, frontmatter name/description only) and give every step a "Skills:" line naming the skill(s) that genuinely match that step's work, or "Skills: none" — match on task shape, don't pad. Then cross-check TODO.md at the repo root (if present): propose (don't auto-include) any cheap adjacent TODO item touching the same files as an optional step, and list every TODO item this plan would complete, partially complete, obsolete, or conflict with under "## TODO impacts" at the bottom of the plan. Save to .workflow/plan.md.
```

**Close with the next-step card** (format in `SKILL.md`) — mandatory, no substitute. A conversational closer ("want me to proceed?") is not the card; if in doubt, print it.
