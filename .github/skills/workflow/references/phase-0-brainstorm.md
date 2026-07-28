# Phase 0 — Brainstorm — `workflow brainstorm` / `workflow improve`

> ⚠️ **Invoke the `workflow` skill before acting on this file** — reading it raw is how the closing next-step card gets dropped.

Seat: **brainstorm partner** — mapping in `ROUTING.md`. Rationale: knowledge-shaped dialogue
with no execution payoff — the cheap conversational seat wins; never spend a reviewer- or
heavy-executor-tier model here.

Four things the dialogue depends on and a fresh reader won't infer:

- **`TODO.md` is intake, not truth.** It is the human's scratchpad; `PRODUCT.md` owns product
  state and TODO entries point at it rather than duplicating it. Scratchpad paths, column names
  and assumptions drift against the code — which is why seeding from an item means re-verifying
  it, not trusting it.
- **Ask `ROADMAP.md` a different question than `TODO.md`.** Intake tells you what has already
  been *captured*; the roadmap tells you what has already been *committed*. They fail differently:
  a duplicated todo wastes a brainstorm, while work that quietly contradicts a committed item
  spends a whole cycle going the wrong way. That asymmetry is why a contradiction stops the phase
  instead of being brainstormed past — committed direction is the human's to change.
- **Ask in a way that survives a distracted reader.** This seat is a strong reasoner told to
  prioritise the questions that most change architecture or scope, and its natural output is one
  sentence carrying three decisions and two pieces of jargon. That question isn't wrong — it's
  unanswerable in thirty seconds, and a rushed guess at a scope question costs more than the
  question was ever worth. Simplify the packaging, never the thinking: same question, one
  decision, plain words, options on the table.
- **Downstream reads this file.** Phase 2 and review both work from `.workflow/brainstorm.md` and
  nothing else from this phase. An item considered and rejected here gets quietly re-imported
  later unless it is named under non-goals.

Run the brainstorm directly, or hand it to a fresh session on the brainstorm seat by pasting:

```text
Read PRODUCT.md and DESIGN.md first, if they exist and this touches product direction or UI, so we don't relitigate settled decisions. Also read TODO.md at the repo root if present — it's my intake scratchpad, not a roadmap: if this brainstorm matches a listed item, seed from its user story/purpose/DoD and original details but verify every detail against the current code and treat anything stale as a question rather than a fact; otherwise scan Active Initiatives, Small UI Changes and Open Questions for items touching the same feature or files, list the related ones, and ask which to fold into scope and which to leave out. Never treat an item being listed as approval to implement it — confirm scope with me first. Also read ROADMAP.md at the repo root if present, and ask a different question of it than of TODO.md: TODO.md tells you whether this was already captured, ROADMAP.md whether it is already committed. Say explicitly which of three this is — it belongs to a committed roadmap item (name the item, and treat that item's scope as the boundary), it contradicts one (stop and tell me: changing committed direction is my decision, not something to brainstorm past), or it is genuinely new (say so, and say whether it should become a roadmap item or stay intake). Let's brainstorm before we spec anything: [describe the idea, problem, or need]. Ask me clarifying questions one at a time, prioritizing whichever question's answer would change the architecture or scope the most, and make each one easy to answer in a hurry: one decision per question; the question itself first, in plain language, two sentences at most; the realistic options on their own lines where there are any, so I can answer with a letter; and one line on what changes depending on my answer. Don't use a term I haven't used myself unless you define it in the same breath. Keep the depth in your thinking rather than in the sentence — if a question genuinely can't be asked simply without losing the decision, give me two lines of plain background first, then ask it. When misreading my answer would be expensive, play back what you understood in one line before moving on. Explore 2-3 different approaches with tradeoffs, and push back on any assumption that seems shaky or any scope that seems bigger than the actual need. Don't write a spec yet. When we've converged, save a short summary to .workflow/brainstorm.md: problem statement and scope (including anything folded in from TODO.md), chosen approach, explicit non-goals (including every excluded TODO item by name), and open questions. Start that file with a five-line provenance header: Command, Created (date), Base (current git sha), Inputs (none — a brainstorm has no upstream artifact), Status (complete).
```

## Variants

- **Blind spot pass** (unfamiliar territory, don't know what to ask yet): find unknown unknowns —
  what would an expert here know that isn't known — and explain them before brainstorming
  approaches.
- **Reference instead of prose** (can't describe what's wanted but would recognize it): read the
  named file/library/component as the reference for shape/behavior, then brainstorm how it adapts
  here.
- **Improve** (command: `workflow improve <feature> - goal: <goal>`) — brainstorm seeded by a
  real code audit instead of a blank idea, scoped to one feature and one pass, no sub-agent fan-out, no multi-file plan backlog. Find and read the
  named feature's actual code first. Look for concrete, evidence-backed improvement
  opportunities in it (correctness, tech debt, performance, missing tests, docs/DX) — every
  finding cites `file:line`, no generic suggestions. Weigh each finding against the stated goal:
  drop or clearly mark as tangential anything that doesn't serve it. Present the findings as a
  short table and ask which ones to pursue, same as a normal brainstorm's clarifying-question
  step — don't assume all of them. Once agreed, save the usual summary (problem statement built
  from the goal + selected findings, chosen approach, explicit non-goals including the rejected
  findings and why, open questions) to `.workflow/brainstorm.md` — same file, same shape as a
  regular Phase 0 brainstorm, so `workflow spec` picks it up identically either way.

**Close with the next-step card** (format in `SKILL.md`) — mandatory, no substitute. A conversational closer ("want me to proceed?") is not the card; if in doubt, print it.
