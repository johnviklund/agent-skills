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

`.github/skills/` is the canonical source for skill content. Codex reaches it via direct
symlinks; Copilot reaches it through its own plugin install mechanism, which needs a **separate,
independently-updated copy** — see the update step below, that's the one non-obvious part of
this setup.

**The clone lives at `~/Documents/Projects/agent-skills`.** Every Codex symlink points at that
absolute path, so the clone is load-bearing infrastructure, not a scratch checkout — moving or
deleting it silently breaks all five skills in Codex (Copilot is unaffected, since it installs
from GitHub into its own cache). If it must move, re-point the symlinks in the same change.

- **Codex CLI:** `~/.codex/skills/<name>` is a symlink straight into this repo's
  `.github/skills/<name>`, one per skill — currently `checkup`, `evals`, `memory.compact`,
  `memory.remember`, and `workflow`. Edits here are live for Codex immediately, no commit or
  reinstall needed. Codex discovers personal skills **only** in `$CODEX_HOME/skills`
  (`~/.codex/skills` by default) and a project's `.codex/skills/` — it does *not* read
  `~/.agents/skills/`, so a skill dropped there alone will load in Copilot and never appear in
  Codex. Verify with `ls -la ~/.codex/skills/` (every link must resolve) and `/skills` in a
  session.
- **Copilot CLI:** installed as a real plugin via `copilot plugin install jviklun1/agent-skills`
  (registered in `~/.copilot/config.json`'s `installedPlugins`, confirm with `copilot plugin
  list`). This is **not** a symlink — Copilot downloads a snapshot of the repo into its own cache
  (`~/.copilot/installed-plugins/_direct/jviklun1--agent-skills`), so a local edit is invisible to
  Copilot until it is **committed, pushed, and reinstalled**. Refresh with a **clean reinstall**,
  not `plugin update`:
  `copilot plugin uninstall agent-skills && copilot plugin install jviklun1/agent-skills`.
  A root-level `skills/` folder (symlinking into `.github/skills/`) exists only so Copilot's
  installer finds the skills at the path it expects; `.github/skills/` stays the one place to
  actually edit.
  **Why uninstall+install instead of `copilot plugin update`:** the installer materializes
  *both* the top-level `skills/` shims (dereferenced into real files) and the canonical
  `.github/skills/` tree into its snapshot. Copilot only scans `skills/`, so the `.github/skills/`
  copy is inert — but an incremental `plugin update` has been observed to refresh `skills/` while
  leaving the `.github/skills/` copy **stale**, drifting the two trees apart. A full uninstall+install
  wipes the snapshot and rematerializes both from one commit, so they can't drift.
  Note: `copilot plugin install` currently warns that direct repo/URL/local-path installs are
  deprecated in favor of `plugin@marketplace` installs — if that stops working in a future
  Copilot CLI release, register this repo as a marketplace instead
  (`copilot plugin marketplace add jviklun1/agent-skills`) and install from there.

### Updating a skill

1. Edit under `.github/skills/<name>/` (never the root `skills/` shims — they're symlinks).
2. Commit and push. Codex is already live at this point; Copilot is not.
3. `copilot plugin uninstall agent-skills && copilot plugin install jviklun1/agent-skills`.
4. Verify both: `diff -rq ~/.codex/skills/<name> .github/skills/<name>` and
   `diff -rq ~/.copilot/installed-plugins/_direct/jviklun1--agent-skills/.github/skills/<name> .github/skills/<name>`.

Earlier setup attempt for the record: hand-creating a directory under
`~/.copilot/installed-plugins/local/` with a `.codex-plugin/plugin.json` does **not** register a
plugin — it's invisible to `copilot plugin list` and never actually loads. The only way to
register a plugin is to run `copilot plugin install <source>` (or `plugin marketplace add` +
install) and let Copilot write its own `config.json` entry.

## Adding a new global skill

Add a new folder under `.github/skills/<name>/SKILL.md`, symlink it from `skills/<name>` at the
repo root, commit and push, then clean-reinstall the Copilot plugin
(`copilot plugin uninstall agent-skills && copilot plugin install jviklun1/agent-skills`) and add
a matching symlink under `~/.codex/skills/<name>` pointing at the absolute path
`~/Documents/Projects/agent-skills/.github/skills/<name>`.

**Known gotcha — Copilot silently drops skills with a long `description`.** Confirmed
empirically: a description field somewhere between ~1033 and ~1078 characters causes Copilot's
plugin loader to install the plugin successfully (no error) but silently omit that one skill from
`copilot skill list` — the skill name and content aren't the cause (tested independently), only
description length is. Codex is unaffected (it reads `SKILL.md` directly, no such limit
observed). Keep each skill's `description` under ~900 characters to stay safely clear of this,
and after adding or editing a skill, verify it actually registered:
`copilot skill list --json | grep '"name": "<your-skill>"'` — don't just trust the "Updated N
skills" success message, since that prints even when a skill was silently dropped.
