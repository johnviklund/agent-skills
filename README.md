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

## How this repo is wired up

This repo is the canonical source. Both CLIs reach it via a symlink into their own global skill
path — the skill files here are the single source of truth; nothing should be edited in place
anywhere else.

- **Codex CLI:** `~/.codex/skills/memory.remember` and `~/.codex/skills/memory.compact` are
  symlinks into this repo's `.github/skills/`.
- **Copilot CLI:** registered as a local plugin (`~/.copilot/installed-plugins/local/agent-memory`)
  whose `skills/` entries symlink into this repo's `.github/skills/`.

## Adding a new global skill

Add a new folder under `.github/skills/<name>/SKILL.md`, commit and push, then symlink it into
both CLIs' global paths (or add it to the `agent-memory` plugin's `skills/` folder if it's
related to the existing pair, or register a new plugin folder if it's an unrelated skill).
