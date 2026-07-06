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
- **`multi-agent-workflow`** — a personal five-phase solo-dev workflow (brainstorm → spec →
  audit & plan → execute → review → wrap-up) split across Codex CLI and Copilot CLI on purpose
  (separate token/quota pools). This is the canonical source for that workflow — there is no
  separate markdown doc to keep in sync; edit this skill directly when the workflow's shape
  changes.

## How this repo is wired up

`.github/skills/` is the canonical source for skill content. Codex reaches it via a direct
symlink; Copilot reaches it through its own plugin install mechanism, which needs a **separate,
independently-updated copy** — see the update step below, that's the one non-obvious part of
this setup.

- **Codex CLI:** `~/.codex/skills/memory.remember` and `~/.codex/skills/memory.compact` are
  symlinks straight into this repo's `.github/skills/`. Edits here are live for Codex immediately
  after a commit — no extra step needed.
- **Copilot CLI:** installed as a real plugin via `copilot plugin install jviklun1/agent-skills`
  (registered in `~/.copilot/config.json`'s `installedPlugins`, confirm with `copilot plugin
  list`). This is **not** a symlink — Copilot downloads a snapshot of the repo into its own cache
  (`~/.copilot/installed-plugins/_direct/jviklun1--agent-skills`), so after editing a skill here
  and pushing, run `copilot plugin update agent-skills` to refresh Copilot's copy. A root-level
  `skills/` folder (symlinking into `.github/skills/`) exists only so Copilot's installer finds
  the skills at the path it expects; `.github/skills/` stays the one place to actually edit.
  Note: `copilot plugin install` currently warns that direct repo/URL/local-path installs are
  deprecated in favor of `plugin@marketplace` installs — if that stops working in a future
  Copilot CLI release, register this repo as a marketplace instead
  (`copilot plugin marketplace add jviklun1/agent-skills`) and install from there.

Earlier setup attempt for the record: hand-creating a directory under
`~/.copilot/installed-plugins/local/` with a `.codex-plugin/plugin.json` does **not** register a
plugin — it's invisible to `copilot plugin list` and never actually loads. The only way to
register a plugin is to run `copilot plugin install <source>` (or `plugin marketplace add` +
install) and let Copilot write its own `config.json` entry.

## Adding a new global skill

Add a new folder under `.github/skills/<name>/SKILL.md`, symlink it from `skills/<name>` at the
repo root, commit and push, then run `copilot plugin update agent-skills` and add a matching
symlink under `~/.codex/skills/<name>`.
