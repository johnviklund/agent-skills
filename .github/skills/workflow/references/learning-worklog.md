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
- [durable→eval] <shape> — <what the golden case proves> (artifacts: <which .workflow files / diff / output>)
- [drop] <one-off noise>
```

### `[durable→eval]` — every run should exam future models

Evals are a side effect of shipping, never separate work: each run's approved artifacts become
golden test cases that future models must pass before earning a seat in the routing tables. Cases
are filed under the **seat** they exam — the same seat names `ROUTING.md` maps — while the *shape*
of the case lives in the filename, so one seat can hold more than one kind of exam:

| Case shape | Seat directory | Input → approved output |
|---|---|---|
| `spec` | `default-executor` | this run's `brainstorm.md` → the *approved* `spec.md` |
| `plan-audit` | `strict-reviewer` | the approved `spec.md` → the *approved* `plan.md` |
| `code-review` | `strict-reviewer` | a diff containing a *confirmed* P0/P1 → the finding that caught it |
| `mechanical-transform` | `mechanical-lane` | a transform prompt with known-correct output |

Tag the line with the **shape**; wrap resolves the seat directory from this table. `code-review`
cases are the most valuable — a candidate reviewer must catch everything the incumbent caught.
`brainstorm-partner` and `heavy-executor` are real seats with no admitted case shape yet; leave
those directories absent rather than inventing a shape to fill them.

**Admission test — deposit only discriminating cases:** a case earns a slot only if it would
plausibly separate models (the approved output required real judgment, or a model actually got
it wrong first). Routine cases teach nothing. **Cap: ~15 cases per case shape, rolling** — per
shape, not per directory, so `strict-reviewer` holds up to ~15 `plan-audit` cases *and* ~15
`code-review` cases. When full, a new case displaces the weakest *of its own shape*, never one of
another shape and never a plain append: ranking a plan audit against a code review is meaningless
because they exam different capabilities, and a shared pool would let one shape crowd the other
out of the exam entirely. Wrap performs the deposit (see
`references/wrap.md`); tag the line any time during the run, at latest before wrap.

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
  ## YYYY-MM-DD · <one-line what> · <model>
  - <1–4 terse bullets: what shipped / changed>
  - Commits: <sha> <sha> ... (+ <other-repo> <sha> if it spanned repos)
  - Review: <verdict> @ <reviewed sha>   (workflow runs only — omit when no review ran)
  - Why: <one line>
  ```

  For a completed `workflow realign` evidence review that accepted no redline, the entry may use
  `Commits: none — docs confirmed current`; omit `Review:` because this is not a standard workflow run.

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
