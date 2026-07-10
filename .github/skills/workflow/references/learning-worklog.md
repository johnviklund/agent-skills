# Learning loop & Worklog — `workflow learn` / `workflow log`

## Learning loop — `workflow learn`

Commits save *what* changed; `MEMORY.md`, skills, and `DESIGN.md` save *why*. This is the point
of the workflow, not an afterthought: **solve a real problem → remember it. Do the same kind of
thing 3+ times → turn it into a skill.**

Append one line per learning to `.workflow/learnings.md` before any `/clear` or `/compact`, and
any time something worth keeping gets solved — don't wait for wrap-up:

```text
## Phase N — <name> (YYYY-MM-DD)
- [durable→memory] <the fix, gotcha, or decision>
- [durable→skill] <the transferable principle, stripped of concrete schema/names/logic>
- [durable→design] <the UI pattern/convention/token decision>
- [drop] <one-off noise>
```

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

**Anti-bloat is the whole point** (learned from bounded-memory designs like Hermes): the file is
capped and rolls off. Never let it grow into a second memory file that confuses future sessions.

- **Cap:** keep roughly the **15 most recent entries** (about one screen). Before appending, if
  there are already ~15 `## ` entries, **delete the oldest ones** — they are preserved forever in
  git history of the file and in the commits they cite. Rolling off is deletion, not archival.
- **Entry = pointer, not payload.** One entry per unit of work (a workflow run, or an ad-hoc
  session). Shape:

  ```markdown
  ## YYYY-MM-DD · <one-line what> · <CLI/model>
  - <1–4 terse bullets: what shipped / changed>
  - Commits: <sha> <sha> ... (+ <other-repo> <sha> if it spanned repos)
  - Why: <one line>
  ```

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
