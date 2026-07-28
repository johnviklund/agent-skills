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
- **Cross-vendor review.** The strict reviewer should be a different vendor from whichever model
  wrote the code. Each run records its writer, so the reviewer detects a same-vendor pairing and
  declares degraded mode itself instead of relying on being told.
- **Files are the state machine.** Every phase reads and writes `.workflow/*.md`, so any fresh
  session re-grounds from disk instead of trusting its own memory. Every phase persists *as it
  goes* — findings as they're confirmed, plan steps as they settle, execution state after every
  step — so running out of context costs warm cache and nothing else.
- **Learning compounds.** Each run routes durable lessons to memory/skills/design docs, keeps a
  bounded worklog, and deposits **golden eval cases** — approved specs, plans, and
  caught-bug/finding pairs — so future models must pass an exam built from your real work
  before earning a seat.
- **Gates where mistakes are expensive.** Clarifying questions before ambiguous or risky work,
  diff-by-diff approval on schema/contract edits, review with P0–P3 verdicts, patch loops bounded
  at three cycles, and a wrap that refuses to ship code the review never saw.

## Repo layout

| File | Role | You edit it? |
|---|---|---|
| `SKILL.md` | The hub: invocation, state machine, seats & invariants, per-command index | No |
| `ROUTING.md` | **Your mapping**: seat → (harness · model · effort), fallbacks, harness verbs | **Yes — this is the whole setup** |
| `references/*.md` | Full instructions per command, loaded one-per-invocation | No |

`SKILL.md` and `references/` contain no vendor names by design; a brand name in either is a bug.
They name only generic verbs — *reset*, *compact*, *context meter*, *model picker*, *explicit
invocation* — and `ROUTING.md`'s **Harness verbs** table holds the literal command per tool. This
README is reader-facing and names tools freely. The shipped `ROUTING.md` is the author's real
mapping (Codex CLI + Copilot CLI) plus an example Claude Code pairing for forks.

## What a run writes

Run scratch lives in `.workflow/` (gitignored). Every artifact opens with a five-line provenance
header, and the state machine reads it rather than guessing from which files exist:

```
Command: workflow spec
Created: 2026-07-28
Base:    <git sha when the file was created>
Inputs:  .workflow/brainstorm.md @ <its own Base sha>
Status:  drafting        # → complete when the phase writes its closing section
```

`Status: drafting` always means *resume that phase*, never advance — so a half-drafted plan can't
be mistaken for a finished one, and a review that died at 90% is distinguishable from one that
never ran. `Inputs` and `Base` let a phase notice its input went stale and ask, instead of
silently building on it.

Durable output goes to the repo: commits, `WORKLOG.md`, `MEMORY.md`, `TODO.md`, product docs, and
golden eval cases at `evals/<seat-slug>/<shape>-<YYYY-MM-DD>-<slug>.md` — seat slugs matching
`ROUTING.md` (`strict-reviewer`, `default-executor`, …) and shape one of `spec`, `plan-audit`,
`code-review`, `mechanical-transform`.

## Install

The skill is a plain folder. Put it where your CLI looks, then rewrite `ROUTING.md`.

**One-copy layout (recommended).** No single directory is read by all three, but each harness
follows a symlink, so keep one canonical copy and point the others at it:

```bash
mkdir -p .agents/skills && cp -r workflow .agents/skills/workflow   # canonical copy
mkdir -p .claude/skills && ln -s ../../.agents/skills/workflow .claude/skills/workflow
mkdir -p .codex/skills  && ln -s ../../.agents/skills/workflow .codex/skills/workflow
echo '.workflow/' >> .gitignore
```

Copilot CLI reads `.agents/skills/` directly, so the canonical copy already covers it.

Or install per harness:

| Harness | Project location | Personal location | Verify | Explicit invocation |
|---|---|---|---|---|
| **Claude Code** | `.claude/skills/workflow/` | `~/.claude/skills/workflow/` | `/workflow status` | `/workflow` — the directory name *is* the command |
| **Codex CLI** | `.codex/skills/workflow/` | `$CODEX_HOME/skills/workflow/` (defaults to `~/.codex/skills/`) | `/skills` lists it | `$workflow status` |
| **Copilot CLI** | `.github/skills/`, `.claude/skills/` or `.agents/skills/` | `~/.copilot/skills/` or `~/.agents/skills/` | `/skills list`, `/skills info workflow` | description-matched; as a plugin, `/<plugin>:workflow` |

Codex CLI does **not** read `.agents/skills/` — it discovers only `$CODEX_HOME/skills`
(`~/.codex/skills` when `CODEX_HOME` is unset) plus the project's `.codex/skills/`. A copy left
solely in `.agents/skills/` loads in Copilot CLI and silently never appears in Codex.

Then:

1. **Rewrite `ROUTING.md`** for your CLIs and models — seat table, phase table, harness verbs.
   That is the entire configuration.
2. **Smoke test**: `workflow status` in each CLI (does it load?), `workflow next` (does it read
   `ROUTING.md`?), then one toy `workflow brainstorm` → `workflow wrap` run end to end.

**Verify locally before trusting the paths above.** Skill discovery has moved before and is
version-dependent — some builds gated skills behind a feature flag. `/skills` (Codex, Copilot)
and `/doctor` (Claude Code) are the ground truth on your machine; the tables above were verified
against Codex CLI 0.145.0 and Copilot CLI 1.0.75. If a
skill loads but never triggers, check its description isn't being shortened out by a crowded
skill list; if Codex ignores it, check `~/.codex/config.toml` for a `[[skills.config]]` entry
disabling it.

## Update

```bash
cd .agents/skills/workflow && git pull      # or re-copy the folder
```

Keep your own `ROUTING.md` — it is the only file you edit, and an update should never overwrite
it. Copy the incoming `ROUTING.md` only to pick up new *sections* (e.g. the harness-verbs table),
then re-enter your own mappings. After updating, run `/skills reload` in Copilot CLI, or restart
the session in Codex and Claude Code, then re-run the smoke test.

If you edit the skill itself: keep `SKILL.md` under ~205 lines, keep vendor names out of
`SKILL.md` and `references/`, and grep the **whole** folder — `ROUTING.md` and this README
included — when you retire a command, or you will leave dangling references behind.

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
| `workflow review` | Strict senior review, empirical verification, P0–P3 verdict; patch cycle bounded at 3 |
| `workflow wrap` | Final checks, commit/push, reconcile product docs, route learnings, deposit eval cases, update `TODO.md` + worklog, clear scratch |
| `workflow todo <idea>` | Capture an idea into `TODO.md`, well-placed and well-shaped |
| `workflow bootstrap` | Stand up `AGENTS.md`/`MEMORY.md`/`TODO.md` and repo conventions in a fresh project |
| `workflow status` / `next` / `log` / `learn` | Where am I / what's the next step card / ad-hoc worklog entry / capture a learning |

Every phase response ends with a **next-step card**: reset-or-continue, which harness/model/
effort (from `ROUTING.md`), what to read, and the exact line to paste. There is no compact
command — resetting is lossless and safe at any context fullness, so it replaced compaction
entirely.

## Companion skills (optional, separate)

- **checkup** — read-only workspace health report; audits eval-set health and flags unexamined
  models.
- **evals.run** — exams a candidate model on a seat's golden cases and writes a scorecard;
  seat changes in `ROUTING.md` are then a deliberate, evidenced edit.

The triad: *workflow deposits → checkup detects → evals.run exams.*

## Design constraints (on purpose)

Single-voice: no reviewer personas, no self-orchestrated sub-agents (a harness's parallel mode
is allowed only on read-only seats, for breadth). Bounded everything: worklog ~15 entries, eval
sets ~15 cases per case shape, patch loops max 3 cycles. The hub stays under ~205 lines; growth
means a new reference file, not a longer hub. When editing instructions: each rule stated once,
outcomes over step prescription, absolutes only for true invariants.

## License

Add your license of choice before publishing (MIT is a natural fit for a skill like this).
