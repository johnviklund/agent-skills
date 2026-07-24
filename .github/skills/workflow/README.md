# workflow — a five-phase coding workflow skill for CLI coding agents

A vendor-neutral [agent skill](https://code.claude.com/docs/en/skills) that runs a disciplined
solo-dev loop across whatever CLI coding agents you use: **brainstorm → spec → audit & plan →
execute → review → wrap**. Models fill **seats** (roles with an output contract); you map seats
to your own CLIs and models in one file. Born as a personal two-CLI workflow; open-sourced
because the shape turned out to be portable.

## Why it exists

- **Seats, not vendors.** The workflow speaks in roles — brainstorm partner, default executor,
  heavy executor, mechanical lane, strict reviewer. `ROUTING.md` maps those to your actual
  tools. Swapping models is a one-file edit; the workflow never changes.
- **Cross-vendor review.** The strict reviewer is a different vendor from whichever model wrote
  the code — an independent family catches blind spots the writer's siblings share.
- **Files are the state machine.** Every phase reads and writes `.workflow/*.md`, so any fresh
  session re-grounds from disk instead of trusting its own memory. Execution persists state
  after *every* step; a context compaction (manual or automatic) can never strand a run.
- **Learning compounds.** Each run routes durable lessons to memory/skills/design docs, keeps a
  bounded worklog, and deposits **golden eval cases** — approved specs, plans, and
  caught-bug/finding pairs — so future models must pass an exam built from your real work
  before earning a seat.
- **Gates where mistakes are expensive.** Clarifying questions before ambiguous or risky work,
  diff-by-diff approval on schema/contract edits, review with P0–P3 verdicts, patch loops with
  bounds.

## Repo layout

| File | Role | You edit it? |
|---|---|---|
| `SKILL.md` | The hub: invocation, state machine, seats & invariants, per-command index | No |
| `ROUTING.md` | **Your mapping**: seat → (harness · model · effort), fallbacks, harness quirks | **Yes — this is the whole setup** |
| `references/*.md` | Full instructions per command, loaded one-per-invocation | No |

`SKILL.md` and `references/` contain no vendor names by design; a brand name in either is a
bug. This README is reader-facing and names tools freely. The shipped `ROUTING.md` is the author's real mapping (Codex CLI +
Copilot CLI) plus an example Claude Code pairing for forks.

## Install

1. Put this folder where your CLI loads skills from:
   - **Copilot CLI**: `.github/skills/workflow/` in your repo (or install as a plugin)
   - **Codex CLI**: symlink into `~/.codex/skills/workflow`
   - **Claude Code**: `.claude/skills/workflow/` (project) or `~/.claude/skills/workflow/`
2. Rewrite `ROUTING.md` for your CLIs and models. That's the entire configuration.
3. Add `.workflow/` to your repo's `.gitignore` (run scratch lives there).

Smoke test: `workflow status` in each CLI (loads?), `workflow next` (reads `ROUTING.md`?), then
one toy `workflow brainstorm` → `workflow wrap` run end to end.

## Commands

Invocation is deliberate — a message starting `workflow <command>` (or `/workflow <command>`);
casual mentions of "plan" or "review" never trigger it.

| Command | What it does |
|---|---|
| `workflow brainstorm <idea>` | Interactive dialogue → `brainstorm.md`; reviews your `TODO.md` for related items |
| `workflow improve <feature> - goal: <goal>` | Brainstorm seeded by a real code audit |
| `workflow spec` | Verified technical spec → `spec.md` |
| `workflow plan` | Cross-vendor audit → file-by-file checklist with per-step verification + suggested skills → `plan.md` |
| `workflow execute` | One step at a time: edit, check, diff, commit, persist state |
| `workflow review` | Strict senior review, empirical verification, P0–P3 verdict; bounded patch cycle |
| `workflow wrap` | Final checks, commit/push, route learnings, deposit eval cases, update `TODO.md` + worklog, clear scratch |
| `workflow todo <idea>` | Capture an idea into `TODO.md`, well-placed and well-shaped |
| `workflow compact` | Persist-first context compaction mid-run; prints the exact compact line to paste |
| `workflow status` / `next` / `log` / `learn` | Where am I / what's the next step card / ad-hoc worklog entry / capture a learning |

Every phase response ends with a **next-step card**: reset-or-continue, which harness/model/
effort (from `ROUTING.md`), what to read, and the exact line to paste.

## Companion skills (optional, separate)

- **checkup** — read-only workspace health report; audits eval-set health and flags unexamined
  models.
- **evals.run** — exams a candidate model on a seat's golden cases and writes a scorecard;
  seat changes in `ROUTING.md` are then a deliberate, evidenced edit.

The triad: *workflow deposits → checkup detects → evals.run exams.*

## Design constraints (on purpose)

Single-voice: no reviewer personas, no self-orchestrated sub-agents (a harness's parallel mode
is allowed only on read-only seats, for breadth). Bounded everything: worklog ~15 entries, eval
sets ~15 cases per seat, patch loops max 3 cycles. The hub stays under ~200 lines; growth means
a new reference file, not a longer hub. When editing instructions: each rule stated once,
outcomes over step prescription, absolutes only for true invariants.

## License

Add your license of choice before publishing (MIT is a natural fit for a skill like this).
