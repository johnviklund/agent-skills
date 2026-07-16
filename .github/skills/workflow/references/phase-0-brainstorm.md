# Phase 0 — Brainstorm — `workflow brainstorm` / `workflow improve`

Seat: **brainstorm partner** — mapping in `ROUTING.md`. Rationale: knowledge-shaped dialogue
with no execution payoff — the cheap conversational seat wins; never spend a reviewer- or
heavy-executor-tier model here.

Read `PRODUCT.md`/`DESIGN.md` if they exist and this touches product direction or UI, then run
the brainstorm directly — ask clarifying questions one at a time (prioritize whichever answer
would change architecture/scope the most), explore 2-3 approaches with tradeoffs, push back on
shaky assumptions or oversized scope. Don't write a spec yet. When converged, save a short
summary (problem statement, chosen approach, explicit non-goals, open questions) to
`.workflow/brainstorm.md`.

**TODO intake.** Also read repo-root `TODO.md` if present — the human's intake scratchpad, never
a roadmap or product truth (`PRODUCT.md` owns product state; TODO entries point at it, they don't
duplicate it). Two directions:

- **Topic matches a TODO item/initiative** → seed the brainstorm from its user story / purpose /
  definition of done and original details instead of re-deriving from scratch — but verify the
  details against the current codebase first (paths, column names, and assumptions in a
  scratchpad drift); treat stale details as questions, not facts.
- **Otherwise** → scan Active Initiatives, Small UI Changes, and Open Questions for items
  touching the same feature/files, list the related ones, and ask which to fold into scope and
  which to leave. Record the outcome in `.workflow/brainstorm.md`: folded items go into the
  problem statement/scope; consciously excluded ones go under non-goals *by name*, so Phase 2
  and review don't re-import them.

Honor the file's own header rule: never implement a TODO item just because it's listed —
confirm scope with the human first.

If handing this to a fresh session on the brainstorm seat, paste:

```text
Read PRODUCT.md and DESIGN.md first, if they exist and this touches product direction or UI, so we don't relitigate settled decisions. Also read TODO.md at the repo root if present — it's my intake scratchpad, not a roadmap: if this brainstorm matches a listed item, seed from its user story/purpose/DoD but verify the details against the current code; otherwise list related TODO items and ask which to fold into scope and which to exclude (record exclusions by name under non-goals). Let's brainstorm before we spec anything: [describe the idea, problem, or need]. Ask me clarifying questions one at a time, prioritizing whichever question's answer would change the architecture or scope the most. Explore 2-3 different approaches with tradeoffs, and push back on any assumption that seems shaky or any scope that seems bigger than the actual need. Don't write a spec yet. When we've converged, save a short summary (problem statement, chosen approach, explicit non-goals, open questions) to .workflow/brainstorm.md.
```

## Variants

- **Blind spot pass** (unfamiliar territory, don't know what to ask yet): find unknown unknowns —
  what would an expert here know that isn't known — and explain them before brainstorming
  approaches.
- **Reference instead of prose** (can't describe what's wanted but would recognize it): read the
  named file/library/component as the reference for shape/behavior, then brainstorm how it adapts
  here.
- **Improve** (command: `workflow improve <feature> - goal: <goal>`) — brainstorm seeded by a
  real code audit instead of a blank idea, loosely inspired by shadcn/improve but scoped to one
  feature and one pass, no sub-agent fan-out, no multi-file plan backlog. Find and read the
  named feature's actual code first. Look for concrete, evidence-backed improvement
  opportunities in it (correctness, tech debt, performance, missing tests, docs/DX) — every
  finding cites `file:line`, no generic suggestions. Weigh each finding against the stated goal:
  drop or clearly mark as tangential anything that doesn't serve it. Present the findings as a
  short table and ask which ones to pursue, same as a normal brainstorm's clarifying-question
  step — don't assume all of them. Once agreed, save the usual summary (problem statement built
  from the goal + selected findings, chosen approach, explicit non-goals including the rejected
  findings and why, open questions) to `.workflow/brainstorm.md` — same file, same shape as a
  regular Phase 0 brainstorm, so `workflow spec` picks it up identically either way.

Close with the next-step card (format in `SKILL.md`).
