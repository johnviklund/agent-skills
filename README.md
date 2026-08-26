# agent-skills

Global, cross-repo agent skills — not tied to any single project. Reachable from every repo,
from both Codex CLI and GitHub Copilot CLI.

## Skills

- **`memory.remember`** — capture durable learnings from the current session and write them
  into the target repo's `MEMORY.md`/`AGENTS.md`/`README.md`, an existing or new skill, and
  `DESIGN.md` (when the repo has one), in one pass.
- **`memory.compact`** — manual, occasional cleanup of a repo's `MEMORY.md`: groups entries by
  topic, flags stale/duplicate/superseded entries and skill-promotion candidates, and writes
  proposal files for review. Never runs automatically.
- **`workflow`** — a personal five-phase solo-dev workflow (brainstorm → spec →
  audit & plan → execute → review → wrap-up) split across Codex CLI and Copilot CLI on purpose
  (separate token/quota pools). This is the canonical source for that workflow — there is no
  separate markdown doc to keep in sync; edit this skill directly when the workflow's shape
  changes.
- **`checkup`** — manual, read-first workspace health check (inspired by a `/checkup` command):
  audits skill hygiene (plugin name collisions, folder-vs-frontmatter name, description length,
  Codex symlink parity, self-publish drift), memory hygiene (MEMORY.md size/staleness/superseded,
  leftover proposals), doc freshness (canonical docs, dead skill references), workspace
  cleanliness (leftover `.workflow` scratch, tracked junk, unpushed work), config health, and
  eval-set health. Reports severity-ranked findings and prioritized fixes; delegates compaction
  to `memory.compact` and eval runs to `evals.run`; applies only opt-in, one-at-a-time safe fixes.
- **`evals`** — runs model exams against the eval golden sets deposited by the `workflow` skill:
  `evals.run <seat> [candidate model]` exams a candidate on one seat's cases (seats: spec, plan,
  reviewer, mechanical), grades with the incumbent strict reviewer plus a human spot check, scores
  quality and cost/latency, and writes a scorecard to `evals/scorecards/`. `evals.list` shows set
  and scorecard status. Routing changes are propose-only — recommends fallback-order edits to the
  `workflow` skill's tables but never applies them.

## How this repo is wired up

**Both Codex CLI and Copilot CLI (and other agent CLIs) read the same shared directory:
`~/.agents/skills/`.** It's managed by a separate, multi-source skill installer that also pulls
third-party skills (e.g. `last30days` from someone else's repo) — its state lives in
`~/.agents/.skill-lock.json`. Because that directory is shared infrastructure for *any* skill
source, it is not simply `git clone`-able as this repo.

`.github/skills/` is still the canonical source for this repo's own skill content (`checkup`,
`evals`, `memory.compact`, `memory.remember`, `workflow`). **The clone lives at
`~/Documents/Projects/skills/agent-skills`** — inside a dedicated `~/Documents/Projects/skills/`
folder that also holds one flat convenience symlink per owned skill
(`~/Documents/Projects/skills/<name>` → `agent-skills/.github/skills/<name>`), so the skill
content is browsable at a short path without duplicating it.

`~/.agents/skills/<name>` is then itself a symlink to `~/Documents/Projects/skills/<name>` — one
per owned skill. This means both Codex and Copilot resolve straight through to the git clone with
**no separate per-tool install step, no reinstall, and no drift between copies**: edit under
`.github/skills/<name>/`, commit, push — done. (An older setup used per-tool Codex symlinks plus
a separately-installed Copilot plugin snapshot requiring reinstall after every change; that's
gone now in favor of the single shared directory both tools already read.)

**Caveat:** the multi-source skill installer that owns `~/.agents/skills/` could in principle
overwrite one of these symlinks with a fresh directory copy during some future "update" pass
across all installed skills. If a skill stops picking up edits, check
`ls -la ~/.agents/skills/<name>` — if it's a plain directory again instead of a symlink, re-run:
`ln -sf ~/Documents/Projects/skills/<name> ~/.agents/skills/<name>`.

### Updating a skill

1. Edit under `.github/skills/<name>/` in `~/Documents/Projects/skills/agent-skills` (or via
   either symlink path — same files).
2. Commit and push. Both Codex and Copilot are live immediately; no reinstall needed.
3. Verify: `readlink ~/.agents/skills/<name>` resolves through
   `~/Documents/Projects/skills/<name>` to `.github/skills/<name>`.

## Adding a new global skill

1. Add a new folder under `.github/skills/<name>/SKILL.md`.
2. Symlink it from `skills/<name>` at the repo root (`../.github/skills/<name>`) — this is only
   for Claude Code's plugin discovery (`.claude-plugin/plugin.json`), unrelated to the local dev
   setup below.
3. Commit and push.
4. Add the two local convenience symlinks so Codex/Copilot pick it up immediately:
   `ln -s agent-skills/.github/skills/<name> ~/Documents/Projects/skills/<name>` then
   `ln -s ~/Documents/Projects/skills/<name> ~/.agents/skills/<name>`.

**Known gotcha — Copilot silently drops skills with a long `description`.** Confirmed
empirically: a description field somewhere between ~1033 and ~1078 characters causes Copilot's
plugin loader to install the plugin successfully (no error) but silently omit that one skill from
`copilot skill list` — the skill name and content aren't the cause (tested independently), only
description length is. Codex is unaffected (it reads `SKILL.md` directly, no such limit
observed). Keep each skill's `description` under ~900 characters to stay safely clear of this,
and after adding or editing a skill, verify it actually registered:
`copilot skill list --json | grep '"name": "<your-skill>"'` — don't just trust the "Updated N
skills" success message, since that prints even when a skill was silently dropped.
