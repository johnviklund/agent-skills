# Phase 0 — Brainstorm — `workflow brainstorm <slug>` / `workflow improve`

> ⚠️ **Invoke the `workflow` skill before acting on this file** — reading it raw is how the closing next-step card gets dropped.

Seat: **brainstorm partner** — mapping in `ROUTING.md`. Rationale: knowledge-shaped dialogue
with no execution payoff — the cheap conversational seat wins; never spend a reviewer- or
heavy-executor-tier model here.

**The slug creates the run.** `workflow brainstorm <slug>` (short, kebab-case, e.g. `auth-refresh`)
creates `.workflow/<slug>/` and everything downstream lives there. Before creating it, run
`git check-ignore -q .workflow` — if the folder is ignored, stop: v2 runs are tracked history and
wrap cannot finish otherwise; ask to remove the ignore rule (and `git add` any existing runs) first. No slug given → propose one from
the idea and confirm it in the first question. A slug that already exists is that run: resume it if
live, offer to unpark it if parked, refuse if done (a finished run is history — start a new slug).
**One brainstorm, one slug:** if the dialogue reveals two runs' worth of work, the summary says so
and the card recommends a second `workflow brainstorm <other-slug>` — never two folders from one dialogue.

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
- **Downstream reads this file.** Phase 2 and review both work from `.workflow/<slug>/brainstorm.md` and
  nothing else from this phase. An item considered and rejected here gets quietly re-imported
  later unless it is named under non-goals. Keep it under ~40 lines: decisions, not the dialogue.
- **This phase decides whether Phase 1 runs.** Phase 2 audits against real code regardless, so the
  closing card recommends `workflow plan` by default and `workflow spec` only when the work is
  schema/SQL/contract-coupled, spans subsystems the brainstorm could not size, or the codebase is
  unfamiliar — and `brainstorm.md` records which, in one line, so `workflow status` can tell.

Run the brainstorm directly, or hand it to a fresh session on the brainstorm seat by pasting:

```text
Read PRODUCT.md and DESIGN.md first, if they exist and this touches product direction or UI, so we don't relitigate settled decisions. Also read TODO.md at the repo root if present — it's my intake scratchpad, not a roadmap: if this brainstorm matches a listed item, seed from its user story/purpose/DoD and original details but verify every detail against the current code and treat anything stale as a question rather than a fact; otherwise scan Active Initiatives, Small UI Changes and Open Questions for items touching the same feature or files, list the related ones, and ask which to fold into scope and which to leave out. Never treat an item being listed as approval to implement it — confirm scope with me first. Also read ROADMAP.md at the repo root if present, and ask a different question of it than of TODO.md: TODO.md tells you whether this was already captured, ROADMAP.md whether it is already committed. Say explicitly which of three this is — it belongs to a committed roadmap item (name the item, and treat that item's scope as the boundary), it contradicts one (stop and tell me: changing committed direction is my decision, not something to brainstorm past), or it is genuinely new (say so, and say whether it should become a roadmap item or stay intake). Let's brainstorm before we spec anything: [describe the idea, problem, or need]. Ask me clarifying questions one at a time, prioritizing whichever question's answer would change the architecture or scope the most, and make each one easy to answer in a hurry: one decision per question; the question itself first, in plain language, two sentences at most; the realistic options on their own lines where there are any, so I can answer with a letter; and one line on what changes depending on my answer. Don't use a term I haven't used myself unless you define it in the same breath. Keep the depth in your thinking rather than in the sentence — if a question genuinely can't be asked simply without losing the decision, give me two lines of plain background first, then ask it. When misreading my answer would be expensive, play back what you understood in one line before moving on. Explore 2-3 different approaches with tradeoffs, and push back on any assumption that seems shaky or any scope that seems bigger than the actual need. Don't write a spec yet. When we've converged, save a short summary (under ~40 lines — decisions, not the dialogue) to .workflow/<slug>/brainstorm.md (create the folder; the slug is the one in my command, or one you proposed and I confirmed): problem statement and scope (including anything folded in from TODO.md), chosen approach, explicit non-goals (including every excluded TODO item by name), open questions, and one line "Next: plan" or "Next: spec" — spec only if this is schema/SQL/contract-coupled, spans subsystems we could not size, or the code is unfamiliar. If I say the idea is good but not now, set Status to parked instead and close with the ✅ parked line (slug + one line on what would unpark it) rather than a next-step card. Otherwise read ROUTING.md and close with the next-step card, its row 2 filled with the concrete vendor, model, effort and context window for that next seat plus its first fallback — never a pointer to ROUTING.md. Start that file with a five-line provenance header: Command, Created (date), Base (current git sha), Inputs (none — a brainstorm has no upstream artifact), Status (complete).
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
  findings and why, open questions) to `.workflow/<slug>/brainstorm.md` — same file, same shape as
  a regular Phase 0 brainstorm, so the next phase picks it up identically either way.

## Parking — `workflow park [slug]`

A run can be parked at any phase, but it ages: a parked `brainstorm.md` keeps for months, a parked
`plan.md` goes stale with the next commit to its files — so park before planning when you can.
Parking sets `Status: parked` in `brainstorm.md` (the run's status of record) and appends one line
under `## Parked` there: date, the phase it was at, what would unpark it. Nothing is deleted. A
parked run is not live: `status` lists it, grounding skips it, and it holds no TODO item — the
folder *is* the item (`TODO.md` keeps only ideas not yet brainstormed). Unparking is
`workflow <phase> <slug>` on the phase it was at: the freshness check then does its job — any
commit since the plan's `Base` that touched a file the plan names means the plan is re-audited
(`workflow plan <slug>`) before anything executes; ancestry alone is not freshness. In chat: `⏸ Parked <slug> at Phase N — <what unparks it>`, then stop.

**Close with the next-step card** (format in `SKILL.md`) — mandatory, no substitute. Read `ROUTING.md` now and fill row 2 with the next seat's concrete vendor · model · effort · context window and first fallback; a seat name or "see ROUTING.md" is a defect. A conversational closer ("want me to proceed?") is not the card; if in doubt, print it.
