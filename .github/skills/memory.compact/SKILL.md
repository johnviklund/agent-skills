---
name: memory.compact
description: >
  Compact and reconcile active MEMORY.md by grouping entries by Topic,
  identifying stale, superseded, duplicate, archive-only, and
  skill-promotion-candidate entries, and producing proposed cleaned files.
  Reads CE artifact directories and existing skill frontmatter as context.
  Run manually when MEMORY.md feels bloated or keeps re-explaining the same
  reusable principle. Recommends a cross-family critic for major reconcile
  passes. Never overwrites MEMORY.md, DESIGN.md, or any SKILL.md directly —
  writes proposals.
---

# memory.compact — Manual Memory Compaction

Reduce MEMORY.md to its essential active entries without losing audit trail or searchable history.

## Memory Layers

- **Active memory:** `MEMORY.md`, read during normal session startup. Keep current,
  behavior-shaping entries only.
- **Agent operating contract:** `AGENTS.md`, checked for rules already promoted
  out of memory.
- **Operator reference:** `README.md`, checked for workflow/setup/output guidance
  already promoted out of memory.
- **Archive memory:** `MEMORY_ARCHIVE.md`, searched by memory workflows but not
  loaded by default. Store old-format entries, long evidence, historical
  baselines, and superseded decisions here.
- **Reusable principle:** a skill under `.github/skills/<name>/SKILL.md`
  (repo-local) or the shared `agent-skills` repo's `.github/skills/<name>/SKILL.md`
  (available from every repo). Checked for entries that have already been promoted, and for entries that
  are strong candidates for promotion (recurred 3+ times, reusable beyond one
  feature, stripped of schema-specific detail).
- **Design doctrine:** `DESIGN.md`, when the repo has one. Checked for
  duplicated UI/design-system prose that should live there instead of in
  `MEMORY.md`.
- **Session breadcrumbs:** `~/.ai-memory/<repo-id>/sessions.log`, deterministic
  hook output. Use as evidence, not as durable memory.

## When to Use

Run `/compact` when:
- MEMORY.md feels bloated or hard to scan
- You suspect contradictions or duplicates across entries
- After a major architecture change that invalidated multiple entries
- Active memory contains long evidence blocks, old baselines, or historical
  material that should move to MEMORY_ARCHIVE.md
- The same reusable principle keeps reappearing across multiple entries and
  should graduate into a skill instead of being re-explained every time
- Periodically, when you feel like it (no fixed cadence)

## Workflow

### 1. Read inputs

Read all of these before starting analysis:

- **MEMORY.md** — the primary active-memory input. Parse all structured entries
  (Topic, Status, Decision, Supersedes, Superseded by, Last confirmed, Source).
- **MEMORY_ARCHIVE.md** — cold-storage input when present. Search it for
  duplicates, superseded context, and a suitable destination section for
  archive-only material. Do not treat the whole archive as active context.
- **CE artifact directories** (read-only context):
  - `docs/brainstorms/` — requirements docs and brainstorm artifacts
  - `docs/plans/` — implementation plans
  - `docs/solutions/` — compound engineering learnings (if present)
  - Scan for content that contradicts or confirms MEMORY.md entries.
- **AGENTS.md** — check for entries that have already been promoted.
- **README.md** — check for operator-facing entries that have already been
  promoted.
- **Existing skill frontmatter** — the `name` + `description` of every skill
  under `.github/skills/*/SKILL.md` and the shared `agent-skills` repo's `.github/skills/*/SKILL.md`.
  Use this to spot `MEMORY.md` entries whose principle already belongs in one
  of them, and to avoid proposing a new skill that duplicates an existing one.
- **DESIGN.md** — when the repo has one, check for entries duplicating
  design-system decisions that already live there, or that should be refined
  into it instead.

### 2. Cross-family critic recommendation

Ask the user:
> **"What model family was used for the most recent `/remember` extract? A different-family review is recommended for major compactions. Continue with the current model for a proposal-only pass? [y/N]"**

- If same family or unsure: warn that cross-family review is better for major
  compactions, but allow a degraded proposal-only pass for solo-dev cleanup.
- If different family: proceed normally.
- Do not block lightweight proposal generation solely because the model family
  is unknown.

### 3. Analyze entries

For each entry in MEMORY.md, classify as:

- **Active** — Status: current, Last confirmed within 90 days, no contradicting
  evidence in CE artifacts or codebase.
- **Stale** — Status: current but Last confirmed > 90 days ago. Flag for
  re-confirmation or supersession.
- **Superseded** — Status: superseded. Keep the supersession chain intact
  but collapse detail or move to archive when it no longer shapes normal work.
- **Duplicate** — Same Topic AND same Decision as another entry. Merge,
  keeping the most recent Last confirmed date.
- **Archive-only** — Useful for search, audit, or historical reconstruction but
  not behavior-shaping enough for startup context. Move or append it to
  MEMORY_ARCHIVE.proposed.md.
- **Contradicted by CE** — A CE artifact (brainstorm, plan, compound note)
  contains information that conflicts with this entry. Flag with the specific
  CE artifact reference.
- **Promoted** — Entry has been promoted to AGENTS.md. Can be removed from
  MEMORY.md (the audit trail lives in git).
- **Operator-promoted** — Entry has been promoted to README.md. Can be removed
  from MEMORY.md unless it still needs a compact active pointer.
- **Skill-promotion candidate** — The entry (or a recurring cluster of entries
  sharing the same underlying principle, seen 3+ times) is reusable beyond
  this one feature/schema and doesn't yet live in any existing skill. Do not
  propose this for a single occurrence — that stays a `MEMORY.md` entry. Note
  which existing skill it should extend, or, only if none fits, that it
  warrants a new one.
- **Skill-duplicated** — The entry restates a principle an existing skill
  (checked in step 1) already covers. Collapse to a short pointer or remove.
- **Design-duplicated** — The entry restates a `DESIGN.md` section (when the
  repo has one). Collapse to a short pointer or remove; the durable version
  belongs in `DESIGN.md`, refined in place.

### 4. Group by Topic

Organize active entries under their Topic join keys. Within each group:
- Active entries come first
- Stale entries get a `<!-- STALE: last confirmed YYYY-MM-DD -->` marker
- Superseded entries are collapsed into a single line:
  `- *Superseded [date]:* [brief decision] → see [new topic ref]`
- Duplicates are merged
- Archive-only entries are omitted from active output and written to the archive
  proposal with enough topic text to remain searchable.

### 5. Cross-entry consistency check

Look for:
- Two entries both claiming `Status: current` on the same Topic
- Entries referencing Supersedes/Superseded by targets that don't exist
- Entries contradicted by current source code (if checkable)
- Entries that could be promoted to AGENTS.md (confirmed 3+ times)
- Entries, or clusters of entries, that could be promoted into an existing or
  new skill (confirmed 3+ times, reusable beyond one feature — see Skill-
  promotion candidate above)
- Duplicated or near-duplicated guidance across `README.md`, `AGENTS.md`,
  `MEMORY.md`, `MEMORY_ARCHIVE.md`, existing skills, and `DESIGN.md`
- Unstructured sections in `MEMORY.md` that bypass the schema
- Stale structured entries whose `Last confirmed` date is old or whose owner is
  now AGENTS/README/skill/DESIGN/archive

### 6. Produce output

The first run for a repo or major cleanup must be proposal-only. Write the
proposed compacted MEMORY.md to **`MEMORY.proposed.md`** in the repo root.

When entries should move to archive, also write **`MEMORY_ARCHIVE.proposed.md`**.
If the repo does not yet have `MEMORY_ARCHIVE.md`, create the proposed archive
from the existing archive format in sibling CX repos or a compact topical format.

**NEVER overwrite MEMORY.md, MEMORY_ARCHIVE.md, DESIGN.md, or any SKILL.md directly.**

The proposed file includes:
- Same header and schema version as MEMORY.md
- Compacted entries organized by section and Topic
- Inline comments explaining what was removed/merged and why
- A summary block at the top:
  ```
  <!-- Compaction summary:
       Input: N entries
       Active: X | Stale: Y | Superseded: Z | Duplicates removed: W
       Contradicted by CE: C | Promotion candidates: P | Skill-promotion candidates: S
       Cross-file overlaps: R README | A AGENTS | M archive | K skill | D design
       Net reduction: N → M entries
  -->
  ```

The proposed archive file includes:
- Existing archive content, preserved unless obviously duplicated
- Moved archive-only entries under topical sections
- A short summary of what moved from active memory

If any skill-promotion candidates were found, also write a short
**`skill-promotion-candidates.proposed.md`** listing each candidate principle
(stripped of schema-specific detail), which skill it should extend (or, only
if none fits, that a new skill is warranted), and the source entries it came
from. Never edit a `SKILL.md` directly from `/compact` — that edit is a
human-reviewed follow-up, the same as accepting `MEMORY.proposed.md`.

Also write a short duplication report into the final user report. It should name
overlaps across `README.md`, `AGENTS.md`, `MEMORY.md`, `MEMORY_ARCHIVE.md`,
existing skills, and `DESIGN.md`, and say which layer should own each overlap.

### 7. Report to user

Print:
- Summary statistics (entries before/after, what was removed)
- Active memory byte/line reduction and archive byte/line change
- Any entries flagged as contradicted by CE artifacts (with artifact references)
- Any promotion candidates for AGENTS.md
- Any skill-promotion candidates, with the target skill named (or "new skill
  warranted" only when the bar is genuinely cleared)
- Cross-file duplication report across README/AGENTS/MEMORY/archive/skills/design
- Instruction: **"Review the diff: `diff MEMORY.md MEMORY.proposed.md`"**
- If archive changes were proposed, instruction:
  **"Review the archive diff: `diff MEMORY_ARCHIVE.md MEMORY_ARCHIVE.proposed.md`"**
- If skill-promotion candidates were found, instruction:
  **"Review the candidates: `cat skill-promotion-candidates.proposed.md`"**
- Instruction: **"Accept by: `mv MEMORY.proposed.md MEMORY.md`"**
- If archive changes were proposed, instruction:
  **"Accept archive by: `mv MEMORY_ARCHIVE.proposed.md MEMORY_ARCHIVE.md`"**

## Rules

- CE artifacts are **read-only input**. `/compact` may flag MEMORY.md entries
  as contradicted by recent CE notes, but it never modifies, tags, or archives
  CE artifacts. CE lifecycle automation is out of scope.
- Tombstone or archive, don't silently delete. Superseded entries are collapsed
  in active memory only when still behavior-shaping; otherwise move them to
  archive with enough context to search.
- Never overwrite MEMORY.md or MEMORY_ARCHIVE.md. Always write proposal files.
- Never edit a `SKILL.md` or `DESIGN.md` directly from `/compact`. Skill and
  design promotion candidates are proposals for the user to apply, the same
  as `MEMORY.proposed.md` — this keeps skill/design changes reviewed rather
  than silently rewritten during a routine memory cleanup.
- Don't propose a new skill for anything short of the 3+ recurrence bar; a
  single striking entry is still just a good `MEMORY.md` entry.
- No state tracking. No cadence. No automatic triggering.
  This is a manual tool the user runs when they want to.
- Aggregates only. Never persist customer-level details.
