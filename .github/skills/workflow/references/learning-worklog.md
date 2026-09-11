# Learning loop & Worklog — `workflow learn` / `workflow log`

> ⚠️ **Invoke the `workflow` skill before acting on this file** — reading it raw is how the closing next-step card gets dropped.

## Learning loop — `workflow learn`

Commits save *what* changed; `MEMORY.md`, skills, and `DESIGN.md` save *why*. This is the point
of the workflow, not an afterthought: **solve a real problem → remember it. Do the same kind of
thing 3+ times → turn it into a skill.**

Append one line per learning to `.workflow/learnings.md` before any reset, and
any time something worth keeping gets solved — don't wait for wrap-up:

```text
## Phase N — <name> (YYYY-MM-DD)
- [durable→memory] <the fix, gotcha, or decision>
- [durable→skill] <the transferable principle, stripped of concrete schema/names/logic>
- [durable→design] <the UI pattern/convention/token decision>
- [durable→eval] code-review — <the P0/P1 that was missed, and by whom> (artifacts: the diff + the finding)
- [drop] <one-off noise>
```

### `[durable→eval]` — a small reviewer exam, and nothing else

Models earn seats on **trial runs**, not exams (protocol in `ROUTING.md`): a candidate takes a
seat for a real run, and the worklog's `Run:`/`Seats:` lines are the evidence. The one exception
is the strict reviewer, because a reviewer miss is expensive and a diff with known findings is a
cheap, honest exam. So the only case shape is `code-review`, filed under `evals/strict-reviewer/`:

| Case shape | Seat directory | Input → what a pass must name |
|---|---|---|
| `code-review` | `strict-reviewer` | a self-contained diff → the confirmed P0/P1 findings in it, one line each |

**Admission test:** a case is deposited only when a P0/P1 was *missed by the writer and caught by
the reviewer*, or *missed by the reviewer and caught later* (the most discriminating kind — file it
when the miss surfaces, even in a later run). Routine findings teach nothing. **Cap: 8 cases,
rolling** — when full, a new case displaces the weakest, never appends past the cap; a set under
the cap is fine, an empty one is fine. There are no spec, plan, or mechanical sets: an approved
plan or spec is not a reusable exam, and those seats prove themselves on trial runs.

Tag the line `[durable→eval] code-review — <what was missed, by whom>` any time during the run;
wrap performs the deposit (see `references/wrap.md`).

Then invoke `memory.remember` (a sibling skill in this same repo, available from both CLIs) to
actually route each line — any time, not only at wrap-up. It reads `MEMORY.md`/`AGENTS.md`/
`README.md`/`DESIGN.md`/every existing skill's frontmatter *and every installed plugin's skill
names* before deciding a destination, so it won't create a skill that collides with one you don't
own. For periodic `MEMORY.md` cleanup, invoke `memory.compact` manually — it never runs on its
own.

## Worklog — `workflow log`

A small, **bounded, rolling** `WORKLOG.md` at the repo root: a newest-first index of what was
built or changed, so any session can backtrack development quickly. It is deliberately **not** a
source of truth and **not** an archive — git is the source of truth for *what* changed, and the
repo's canonical docs (`PRODUCT.md`/`DESIGN.md`/`AGENTS.md`) own *what we're building*.
`WORKLOG.md` only **points into git**; the diffs live in the commits.

**Anti-bloat is the whole point**: the file is
capped and rolls off. Never let it grow into a second memory file that confuses future sessions.

- **Cap:** keep roughly the **15 most recent entries** (about one screen). Before appending, if
  there are already ~15 `## ` entries, **delete the oldest ones** — they are preserved forever in
  git history of the file and in the commits they cite. Rolling off is deletion, not archival.
- **Entry = pointer, not payload.** One entry per unit of work (a workflow run, or an ad-hoc
  session). Shape:

  ```markdown
  ## YYYY-MM-DD · <one-line what> · <vendor> · <model>
  - <1–4 terse bullets: what shipped / changed>
  - Commits: <sha> <sha> ... (+ <other-repo> <sha> if it spanned repos)
  - Review: <verdict> @ <reviewed sha>   (workflow runs only — omit when no review ran)
  - Run: <steps> steps · <cycles> review cycles · <deviations> deviations · <overturned> findings overturned   (workflow runs only)
  - Seats: 0 <vendor·model> · 2 <vendor·model> · 3 <vendor·model> · 4 <vendor·model>   (workflow runs only; add 1 when spec ran)
  - Why: <one line>
  ```

  For a completed `workflow realign` evidence review that accepted no redline, the entry may use
  `Commits: none — docs confirmed current`; omit `Review:` because this is not a standard workflow run.

  The `Run:` and `Seats:` lines are how models are evaluated: read the numbers from `plan.md`
  (checklist length, `## Deviations`, `Writer:` lines) and `review.md` (`## Cycle N` count,
  dispositions marked wrong-by-human) at wrap, so `checkup` can compare a candidate's runs on a
  seat against the incumbent's without re-reading artifacts that wrap deletes.

  The `Review:` field exists because `.workflow/review.md` is deleted at wrap and is not reliably
  in git, so this line is the only surviving trace that the verdict was reached and against which commit.

- **When it's written:**
  - `workflow wrap` appends an entry automatically as part of wrap-up.
  - `workflow log` appends one on demand for an **ad-hoc session that didn't run the full
    workflow** (like a quick fix or a review). Same shape, same cap. This is what keeps the log
    complete instead of only capturing formal runs.
- **Never** paste diffs, file dumps, rationale essays, product/design decisions, or learnings
  into it. Learnings go through `memory.remember`; product/design go to their canonical docs;
  detail lives in git and the session store. If an entry needs more than ~4 bullets, it's too
  much.
- If `WORKLOG.md` doesn't exist yet, create it with a one-line header explaining it's a bounded,
  rolling, git-pointing index, then add the first entry.
