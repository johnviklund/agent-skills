# Bootstrap a new project — `workflow bootstrap [PRD.md]`

> ⚠️ **Invoke the `workflow` skill — do not just read this file.** If you reached this reference without invoking the `workflow` skill this turn, stop and invoke it first. These reference files are the skill's controlling contract (seat, verification bar, and the mandatory closing next-step card); reading them raw skips that contract — which is how required plan/execute steps get silently dropped.


Turns a finished PRD into a workflow-ready repo: the canonical doc set, the empty growing files,
and the hygiene wiring — so the first real `workflow brainstorm` starts on solid ground instead
of a blank folder. Run once, at the start of a project, in an empty or near-empty repo.

Seats: **brainstorm partner** at high effort generates the docs (knowledge-shaped synthesis);
a **strict reviewer** audit pass before the first commit is strongly recommended (mapping in
`ROUTING.md`). The clarify gate applies in full — this writes the project's north star, the
highest-cost-if-wrong artifact there is.

## Procedure

**1. Ground.** Read the given PRD in full (if no path given, look for `PRD.md` at the root; if
none exists, ask — don't bootstrap from nothing). Confirm the repo is empty or near-empty; if
it already has canonical docs, this is the wrong command — say so. `git init` if needed.

**2. Clarify.** One round of questions on anything the PRD leaves ambiguous about *durable
identity*: what's product truth vs. merely the first implementation, explicit non-goals, core
vocabulary, whether the product has a UI (decides DESIGN.md's fate). Roadmap sequencing
questions can wait; identity questions cannot.

**3. Generate the doc set — each doc owns one thing, nothing is authoritative twice:**

| Doc | Owns | Bootstrap state |
|---|---|---|
| `PRODUCT.md` | **The north star**: current state + desired end state, product purpose, users, core objects, workflows, principles, vocabulary, anti-goals | Synthesized from the PRD — the PRD's durable truth lands here |
| `DESIGN.md` | The UI/design system | Synthesized if the PRD implies a UI; otherwise a two-line stub ("no UI yet; create on first UI work") |
| `AGENTS.md` | Operating rules for coding agents: the doc-layer model (this table), write scopes, command contracts | Written fresh; includes this ownership table |
| `ROADMAP.md` | **The sequence**: phased initiatives to implement the PRD, each sized to be one future workflow run (brainstorm→wrap) | Derived from the PRD's scope; items point at `PRODUCT.md`, never restate it |
| `MEMORY.md` | Cross-session environment facts, gotchas, open gaps — the layer of last resort | **Created empty** except a header explaining the entry schema and what does/doesn't belong |
| `TODO.md` | Intake scratchpad for ideas between runs — never a roadmap | **Created empty** except its header rule ("don't implement just because it's listed") and section skeleton |
| `README.md` | Short orientation: what this is + a pointer table to the docs above | A page, not a spec |

Boundary rules that make the set stable: `PRODUCT.md` holds *what and why* (current + desired
end state); `ROADMAP.md` holds *in what order*; `TODO.md` holds *not-yet-decided intake*. A
roadmap item is a pointer to a future workflow run, not a second product description — when the
same sentence appears in two docs, one of them is wrong.

**4. Retire the PRD.** The PRD is frozen input, not a living doc — once `PRODUCT.md` exists,
maintaining both guarantees drift. Move it to `docs/archive/PRD-<date>.md` (or delete it if the
human prefers; it lives in git either way) and note in `PRODUCT.md`'s header that it supersedes
the PRD as of the bootstrap date.

**5. Wire the hygiene.** `.gitignore` with `.workflow/` plus the usual junk (`.DS_Store`,
`.env`, venvs, build output); create `WORKLOG.md` with its bounded-rolling header.

**6. Audit before committing (recommended, strict-reviewer seat, fresh session).** Check every
claim in `PRODUCT.md` and `ROADMAP.md` traces to the PRD or an explicit human answer from step
2 — invented commitments in a north-star doc are the most expensive hallucinations there are.
Check the ownership boundaries don't overlap. Fix, then commit everything as the bootstrap
commit, and append the first `WORKLOG.md` entry.

**7. Hand off.** Close with the next-step card recommending `workflow brainstorm <first
ROADMAP.md initiative>` — from here on, the normal cycle owns everything.

## Afterwards

- ROADMAP.md status updates ride along with wrap's TODO-hygiene step: items a run completed get
  checked off there, with the same boundaries (point at commits, don't grow prose).
- Re-running bootstrap on a bootstrapped repo is an error — refuse and point at `workflow
  brainstorm` / `workflow todo` instead.
