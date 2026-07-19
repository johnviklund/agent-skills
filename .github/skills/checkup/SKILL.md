---
name: checkup
description: >
  Manual workspace checkup: a read-only health report for the current repo and the user's skill
  wiring, inspired by a /checkup command. Use when asked to check, audit, clean up, or tune the
  workspace -- phrases like "checkup", "workspace health", "are my skills in check", "is memory
  compacted", "AGENTS/PRODUCT/DESIGN stale", "skill collisions", or "Codex parity". Checks skill
  hygiene (plugin name collisions, folder-vs-frontmatter name, description length, Codex symlink
  parity, drift), memory hygiene (MEMORY.md size/staleness/superseded, leftover proposals), doc
  freshness (canonical docs, dead skill references), workspace cleanliness (leftover .workflow
  scratch, tracked junk, unpushed work), config health, and eval-set health. Reports
  severity-ranked findings and prioritized fixes;
  delegates compaction to memory.compact and eval runs to evals.run; applies only opt-in safe
  fixes.
---

# checkup

A manual, read-first workspace health check. It answers "is my workspace in good shape?" in one
pass: are my skills wired correctly and not colliding, is memory compacted, are the canonical docs
drifting, is there leftover scratch or junk, is my config sane, are the eval golden sets healthy.
Inspired by a `/checkup` command.

**It reports and recommends; it does not bulldoze.** Everything is read-only by default. The only
things it may change are a short allow-list of *trivially safe* fixes, offered **one at a time**
with explicit approval. Anything risky is proposed or delegated, never auto-applied.

## Invocation

Runs on `checkup` / `/checkup` (a bare `checkup` on its own line is enough). Try `/checkup` first;
if the leading slash is swallowed by the CLI, use the bare `checkup` form. Do not trigger on casual
mentions of "check" elsewhere in a message -- only the explicit prefixed form.

Optional scope words narrow the pass: `checkup skills`, `checkup memory`, `checkup docs`,
`checkup workspace`, `checkup config`, `checkup evals`. With no scope word, run all sections.

## Ground rules

- **Read-only by default.** Gather evidence, then report. Never edit `DESIGN.md`, `PRODUCT.md`, or
  any canonical doc as part of a checkup -- surface drift and let the human decide (this mirrors the
  repo rule that those docs are not edited without an explicit ask).
- **Delegate, don't reimplement.** When memory needs compaction, recommend `memory.compact`; when
  learnings need routing, recommend `memory.remember`; when a model needs examining, recommend
  `evals.run`. Do not re-do their work inside a checkup.
- **Opt-in safe fixes only, one at a time.** See *Safe-fix allow-list*. Ask before each; if the
  user declined the fix appetite, stay pure-report.
- **Honest heuristics.** Staleness of prose docs can't be proven mechanically. Say "worth a human
  skim" with the evidence (last-touched vs. related activity), never "this is wrong".
- **Manual, stateless.** No cadence, no auto-trigger, no state file. The user runs it when they want.
- **Portable.** Assume POSIX shell on macOS/Linux, run from the repo root (cwd). Skip a check
  gracefully (mark it `n/a`) when its inputs don't exist rather than erroring.

## The check battery

Run the sections in order. For each finding pick a severity: **✅ OK**, **⚠️ warn** (worth a look,
not blocking), **🔴 action** (should be fixed). Collect evidence with commands like the following;
adapt paths to what actually exists.

### 1. Skill hygiene

Discover the three skill locations first:

```sh
ls .github/skills/ 2>/dev/null                                   # repo-local skills (this repo)
ls ~/.copilot/installed-plugins/*/*/skills/ \
   ~/.copilot/installed-plugins/_direct/*/skills/ 2>/dev/null    # Copilot plugin/global skills
ls ~/.codex/skills/ 2>/dev/null                                  # Codex skills (symlinks + real dirs)
```

Then check:

- **Name collision with an installed plugin/global skill.** For every repo-local `.github/skills/<name>`,
  is there also a *different* skill named `<name>` installed elsewhere -- a Copilot plugin/global skill,
  or a `~/.codex/skills/<name>` entry? **Ignore a `~/.codex/skills/<name>` that is a symlink pointing
  back to this same repo skill** -- that is intended cross-CLI parity wiring, not a collision (a naive
  name-match false-positives on every skill you've wired into Codex). For genuine matches, **diff the
  `SKILL.md`:** identical content = benign self-publish (note it); **different content = 🔴 harmful
  shadow** (a repo skill hiding a different, often richer, plugin skill). Fix by renaming the repo-local
  one to a repo-specific prefix (e.g. `cx-<name>`) and updating references -- never silently keep the shadow.
- **Folder name ≠ frontmatter `name`.** For each `.github/skills/<dir>/SKILL.md`, confirm `name:`
  matches `<dir>`. A mismatch breaks discovery.
- **Description length.** Flag any skill whose frontmatter `description` exceeds ~900 characters. This
  limit is **confirmed for the Copilot *plugin-install* path** -- the loader silently drops an
  over-length skill (installs with no error, omits it). So it is **🔴 for any skill published/installed
  as a plugin** (it looks installed but isn't; verify `copilot skill list --json | grep '"name": "<skill>"'`).
  For a **repo-local-only** skill loaded directly from `.github/skills/`, discovery tolerates longer
  descriptions -- treat over-length as **⚠️ portability debt** (trim before it is ever published), not a
  hard failure. When unsure, check whether a matching installed plugin or a `copilot-plugins/` packaging
  dir exists.
- **Codex parity.** Codex only loads from `~/.codex/skills/` -- it does not scan a repo's
  `.github/skills/`. For each repo-local skill meant to be available in Codex, confirm a
  `~/.codex/skills/<name>` entry exists and resolves. Missing ones are ⚠️ (Copilot-only until linked).
- **Broken Codex symlinks.** Dangling links whose target no longer exists:
  `find ~/.codex/skills -maxdepth 1 -type l ! -exec test -e {} \; -print`. Each is 🔴.
- **Self-publish drift.** For a skill packaged as its own plugin (e.g. a `.github/copilot-plugins/<name>/`
  whose `skills/<name>` symlinks back to `.github/skills/<name>`), confirm it is still a symlink (single
  source) and that the installed snapshot matches the repo source. A materialized copy or a stale
  installed snapshot is ⚠️ drift.

### 2. Memory hygiene

```sh
wc -lc MEMORY.md 2>/dev/null; grep -c '^### ' MEMORY.md 2>/dev/null
ls MEMORY.proposed.md MEMORY_ARCHIVE.proposed.md skill-promotion-candidates.proposed.md 2>/dev/null
```

- **Size/entry pressure.** Warn when `MEMORY.md` is large (rough guide: > ~40 structured entries or
  > ~40 KB). Recommend running `memory.compact` (do not compact here).
- **Stale entries.** Flag entries whose `Last confirmed:` date is > 90 days before today.
- **Superseded still active.** Any entry with `Status: superseded` still in `MEMORY.md` belongs in
  `MEMORY_ARCHIVE.md`.
- **Leftover proposals.** A lingering `MEMORY.proposed.md` (or archive/skill-promotion proposal)
  means a compaction was generated but never accepted or discarded -- ⚠️, recommend review/`mv` or delete.

### 3. Doc freshness & consistency

- **Canonical docs present.** Note which of `AGENTS.md`, `README.md`, `PRODUCT.md`, `DESIGN.md`,
  `PRD.md`, `MEMORY.md`, `MEMORY_ARCHIVE.md` exist; a repo that has some but is missing an expected
  one is worth a note, not an error.
- **Dead skill references.** Skill names referenced in `AGENTS.md`/README that no longer resolve to a
  real skill (e.g. renamed but the reference wasn't updated). **Scope this to actual skill references** --
  backticked names in the skills-list sentence, or tokens matched against the real skill inventory --
  **not every prefix-shaped token.** Doc *paths* and identifiers often share a skill's prefix (e.g. a
  `cx-agentic-intelligence-poc/` docs folder vs. `cx-*` skills) and will false-positive a naive `grep`.
  🔴 only when a name genuinely referenced *as a skill* exists nowhere (repo-local, plugin, or Codex).
- **Prose-doc staleness (heuristic).** Compare each canonical doc's last commit date against recent
  activity in the area it governs (e.g. `DESIGN.md` vs. churn under `web-ui/`):
  `git log -1 --format=%cs -- DESIGN.md` and `git log --since=... --oneline -- web-ui | wc -l`.
  If a doc is untouched while its area changed a lot, say "worth a human skim" -- never assert it's wrong.
- **Broken internal links.** Doc references to repo paths that don't exist are ⚠️.

### 4. Workspace cleanliness

```sh
ls .workflow/{brainstorm,spec,plan,patch_plan,review,learnings}.md 2>/dev/null   # leftover run scratch
git status --porcelain 2>/dev/null                                        # uncommitted work
git log @{u}.. --oneline 2>/dev/null                                      # unpushed commits
git ls-files | grep -Ei '(^|/)\.DS_Store$|(^|/)\.env$|/node_modules/|/\.venv|\.pyc$' 2>/dev/null
```

- **Leftover `.workflow/*.md` run scratch** from an aborted workflow run -- ⚠️, recommend routing
  learnings (`memory.remember`) then deleting, per the workflow's wrap step. If the leftover
  `learnings.md` contains `[durable→eval]` lines, flag them specifically -- undeposited golden
  cases die with the scratch.
- **Tracked junk / secrets.** A committed `.DS_Store`, `.env`, virtualenv, build output, or `.pyc`
  is 🔴 (secrets) or ⚠️ (junk). A tracked `.env` is always 🔴 -- flag loudly.
- **Uncommitted or unpushed work.** If the repo treats push as its archive/safety net, unpushed
  commits or a dirty tree are ⚠️ worth surfacing.

### 5. Config health

- **`.gitignore` coverage.** Confirm it ignores the usual junk (`.DS_Store`, `.env`, venvs, build
  output). Missing common entries are ⚠️.
- **Light secret sniff.** Only flag obvious committed credential files or `.env`; do not attempt a
  deep secret scan (too noisy) -- point at a dedicated tool if the user wants depth.

### 6. Eval-set health

The `workflow` skill deposits golden cases into `evals/<seat>/` at wrap (seats: `spec`, `plan`,
`reviewer`, `mechanical`); `evals.run` exams candidate models against them. Checkup only audits
the sets and *detects the need* for an exam -- it never runs one (expensive, writes scorecards;
delegate to `evals.run`).

```sh
for s in spec plan reviewer mechanical; do printf '%s: ' $s; ls evals/$s/*.md 2>/dev/null | wc -l; done
grep -rl '\.workflow/' evals/ 2>/dev/null                    # cases pointing at deleted scratch
grep -rhoE 'date: [0-9-]+|^- Date: .*' evals/ 2>/dev/null | sort | tail -3   # newest provenance
ls evals/scorecards/ 2>/dev/null
```

- **Self-containment.** Any case referencing a `.workflow/` path is 🔴 -- that scratch is deleted
  at wrap, so the case is silently worthless. It must inline its input/approved output.
- **Cap respected.** More than ~15 cases in a seat is ⚠️ -- the rolling cap says a new case
  displaces the weakest, never appends past it. Recommend a pruning pass (human decides which).
- **Coverage gaps.** A seat with 0 cases is ⚠️ (the asset isn't compounding); a near-empty
  `reviewer` set is worth calling out specifically -- it's the most valuable set and only grows
  when Phase 4 catches real bugs are tagged.
- **Malformed cases.** A case missing its approved output, grading notes, or provenance is ⚠️ --
  it can't be graded.
- **Staleness (heuristic).** If every case's provenance date is old while the codebase has moved a
  lot since, say "worth a human skim -- cases may test a codebase that no longer exists".
- **Unexamined models (detect-the-need).** Cross-check the models named in the `workflow` skill's
  routing tables against `evals/scorecards/`: a model holding a seat with no scorecard, or a
  scorecard older than the model's entry into the tables, is ⚠️ -- recommend `evals.run <seat>`.
  This mirrors how section 2 detects memory pressure and delegates to `memory.compact`.

## Output format

Print a compact, scannable report grouped by the six sections, each finding one line with its
severity, the evidence, and the fix. Close with a **prioritized action list** (🔴 first) and, if the
user opted into fixes, offer the safe ones one at a time.

```text
## Workspace checkup — <repo> (<date>)

### Skills        ✅/⚠️/🔴  <one line per finding + fix>
### Memory        ...
### Docs          ...
### Workspace     ...
### Config        ...
### Evals         ...

### Recommended next
1. 🔴 <highest-value fix> — <how>
2. ⚠️ <next> — <how / delegate to memory.compact / evals.run / etc.>
...
```

If nothing is wrong, say so plainly ("✅ Workspace is healthy — nothing to act on") instead of
manufacturing findings.

## Safe-fix allow-list (opt-in, one at a time)

Only these may be applied during a checkup, and only after the user approves each:

- Create a missing or repair a broken `~/.codex/skills/<name>` symlink for a repo-local skill.
- Remove a tracked `.DS_Store` (and add it to `.gitignore` if missing).
- Add obviously-missing `.gitignore` entries (`.DS_Store`, `.env`, venvs, build output).
- Delete a leftover `.proposed.md` **only** after confirming its content was already accepted/routed.

Everything else is **propose-only or delegate**: renaming a colliding skill, editing any canonical
doc, compacting memory (`memory.compact`), routing learnings (`memory.remember`), deleting
`.workflow` scratch (route first), running or pruning eval sets (`evals.run` / human decision),
or removing anything that could carry unsaved value.

## Relationship to the other skills

- `memory.compact` — owns the actual MEMORY.md compaction proposal; checkup only *detects the need*.
- `memory.remember` — owns routing learnings; checkup only flags un-routed leftovers.
- `workflow` — owns the dev loop and *deposits* eval golden cases at wrap; checkup flags its
  leftover `.workflow` scratch and unpushed work.
- `evals.run` — owns running the exams and writing scorecards; checkup only audits set health and
  flags unexamined models.

Keep this skill's `description` under ~900 characters (it enforces that rule on others -- it must
pass its own check).
