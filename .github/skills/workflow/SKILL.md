---
name: workflow
description: >
  Trigger ONLY on a message of the form "workflow <command>" or "/workflow <command>" — a
  deliberate invocation of this personal five-phase solo-dev coding workflow; never on casual
  mentions of spec, plan, execute, review, or learn elsewhere in a message. Commands: brainstorm,
  improve, spec, plan, execute, review, learn, wrap, status, next, log, todo, bootstrap;
  bare "workflow" = next.
---

# Workflow

> ⚠️ **This skill must be invoked, not skimmed.** A message of the form `workflow <command>` / `/workflow <command>` is a deliberate invocation: invoke this skill as the first action, before reading references, running commands, or emitting output. Reading `SKILL.md` or `references/*.md` directly is not a substitute for invoking the skill.


A personal five-phase workflow built on **seats** — jobs with an output contract and effort
profile, filled by whatever model currently earns them. Two harnesses stay in the loop on
purpose: separate quota pools buy throughput, and splitting writer from reviewer across vendors
buys independence — a different family catches blind spots the writer's own siblings share.

**This file is the hub, and it is vendor-neutral.** Full instructions per command live in
`references/` (read the one file for the invoked command, per the *Command index*). Seats map to
CLIs, models, efforts, and fallbacks in **`ROUTING.md`** — the only file that names vendors, and
the only one a fork edits.

## Invocation — deliberate, not ambient

Most CLIs reserve `/` for built-ins (skills trigger via description matching), so there is no
*true* `/workflow` command. The required form is a message starting with `workflow` or
`/workflow` plus a command word (see *Command index*). Try `/workflow ...` first; if the CLI
swallows the slash, drop it — both forms are identical. A **bare** `workflow`/`/workflow` is
treated as `workflow next`. **Do not** treat unprefixed mentions of "spec", "plan", "execute",
"review", or "learn" in normal conversation as an invocation.

## Where are we? (re-grounding) — `workflow status`

Report which phase is next without acting, then wait for an explicit `workflow <phase>`. Check
`.workflow/` first — the files are the state machine, more trustworthy than memory. Every artifact
header carries `Status:`, and **`drafting` means resume that phase, never advance past it**. Review
is read-only, so `review.md` is its *only* evidence — never infer "review done" without it. With
every artifact `complete`:

| `.workflow/` | Next |
|---|---|
| empty | Phase 0 — Brainstorm |
| `brainstorm.md` | Phase 1 — Spec |
| `+ spec.md` | Phase 2 — Audit & Plan |
| `+ plan.md`, checklist incomplete | Phase 3 — Execute |
| checklist complete; no `review.md`, or its `Base` ≠ HEAD | Phase 4 — Review |
| `+ patch_plan.md` | mid patch cycle — `references/phase-4-review.md` |
| `review.md`, verdict clean or all findings dispositioned, `Base` = HEAD | wrap-up |

If a given `workflow <phase>` mismatches the file state (e.g. `execute` with no `plan.md`), say
so and ask before proceeding. Also read `ROUTING.md` (which seat this phase wants, and the mapping
every card resolves at print time), `AGENTS.md`, `MEMORY.md`, `PRODUCT.md`/`DESIGN.md` (if
product/UI is in scope), repo-root `TODO.md` (intake scratchpad — relevant to Phase 0, Phase 2's
TODO-impact check, and wrap's TODO hygiene), and `git log --oneline -15` / `git status` as part
of grounding.

## Clarify before running a phase (every phase)

After re-grounding, before the first edit or line of output: **is anything material unclear, or
is a misunderstanding here expensive?** If yes, ask one round of max 2–3 targeted questions
(prioritized by which answer changes the outcome most) and wait. If no, act — this gate must not
turn every phase into an interview. Ask when: **ambiguous target/scope** (matches more than one
thing, or scope reads two ways); **command/state mismatch** (per *Where are we?*);
**high-cost-if-wrong work ahead** (schema/SQL/contract, migrations, deletions — restate the
interpretation in one sentence, "I read this as X — correct?"); **silent assumptions doing heavy
lifting** (surface the assumption instead of building on it). State what *is* understood, then
ask — prefer "I'm about to do X, which I read as Y — confirm or correct" over "what do you
want?". Skip the gate for mechanical, low-risk, clearly-specified work (mechanical-lane edits,
`status`, `next`, `log`, `todo` — which has its own question step). This complements
the in-phase rules (Phase 0 dialogue, Phase 3 stop-on-deviation); it doesn't replace them.

## Seats — roles, not vendors

| Seat | Job | Used by |
|---|---|---|
| Brainstorm partner | Knowledge-shaped dialogue, intake; cheap, conversational | Phase 0, `workflow todo`, wrap |
| Default executor | Specs and everyday logic-bearing implementation; best quality-per-cost | Phase 1, Phase 3 (logic), P1–P3 fixes, wrap |
| Heavy executor | Schema/SQL/contract-coupled edits, hardest multi-file work; best long-horizon agentic model | Phase 3 (schema/contract), P0 fixes |
| Mechanical lane | Transforms with an explicit expected result; cheapest/fastest | Phase 3 (mechanical) |
| Strict reviewer | Deepest read-only reasoning; the workflow's skeptic | Phase 2, Phase 4, patch plans |

Which model fills each seat, in which harness, at what effort, with what fallback chain —
that's `ROUTING.md`. A model earns a seat by winning the eval (`evals.run`), not by novelty.

**Portable invariants (hold regardless of the mapping):**

- **Cross-vendor review.** The strict reviewer is a different vendor from whichever model wrote
  the code. A same-vendor review is a degraded mode — note it when unavoidable.
- **Two harnesses, no double work.** Never run the same step in both harnesses.
- **Availability first.** Check the harness's model picker at session start; walk ROUTING.md's
  fallback chain in order; never retry blindly or silently switch to an auto/default model.
- **Effort scales with risk, not habit.** Use the lowest level that reliably works; mechanical
  work lowest, schema/contract and review highest (exact levels in ROUTING.md). Before *raising*
  effort on a struggling step, first check whether the step is missing a success criterion or
  verification check — a clearer completion bar usually beats more thinking. When a new model
  takes a seat, baseline at the incumbent's effort and test one level lower.
- **Approval scales with blast radius.** Read-only seats need none; mechanical-lane edits
  auto-approve; logic-bearing and schema/contract edits are reviewed diff by diff (the full
  phase→approval map is in ROUTING.md alongside the efforts).
- **Sub-agent/parallel modes are for read-only breadth only.** A harness mode that fans work out
  to internal sub-agents is allowed only on a read-only seat, only when many files/subsystems can
  be checked *independently* — breadth, not depth, and its findings still get *verify, don't
  trust*. Never on anything that writes: parallel writers break one-step-at-a-time and lockstep.

## Context hygiene

Reset the session at every phase handoff (0→1→2→3→4 and
into wrap) — re-ground from `.workflow/*.md` + git instead of dragging the previous phase's
context (or the wrong seat's mindset) forward; the reset is also where the seat's model swap
happens. Mid-phase, reset is the answer too: every phase writes its artifact incrementally, so a
reset costs warm cache and nothing else, and unlike compaction it is safe at any fullness. This file and `references/` name only the verb — *reset*, *compact*, *context meter*, *model
picker*, *explicit invocation*; the literal command per harness is `ROUTING.md`'s **Harness verbs**
table.

## Next recommended step — `workflow next` (or bare `/workflow`)

Every phase response ends with this card — **mandatory, with no substitute**: a conversational
closer ("Next is workflow execute. Want me to proceed?") is not the card. Row 2 is **resolved
from `ROUTING.md` at print time** — the concrete harness, model, and effort for the next seat
plus its first fallback; a seat name or "see ROUTING.md" defeats the card's whole purpose, which
is that the human never opens a file to learn which model to pick. `workflow next` prints the
card on demand without doing phase work (re-ground via *Where are we?* first). Exact shape:

---
### ▶ Next recommended step

**You are here:** Phase N (\<name>) → **Next:** Phase M (\<name>)

| # | Step | Setting |
|---|------|---------|
| 1 | Reset session? | Yes — reset before the handoff (or *No — continue this session*) |
| 2 | Harness · model · effort | resolved values, e.g. "<harness> · <model> · <effort> — fallback: <model> <effort>" |
| 3 | Ground in | exact files to read first |
| 4 | Invoke | the `workflow <phase>` command to send |

**Harness toggles:** autonomy/speed modes per ROUTING.md notes (default: none) · **Sub-agents:** none — except the read-only breadth exception above


Paste next:
```text
<the exact prompt or `workflow <phase>` line to send>
```
---

Defaults: reset Yes at every handoff, No only for continued same-seat work; autonomy/speed
toggles No unless ROUTING.md's harness notes sanction them, never on review seats. Row 3 grounds
in the `.workflow/*.md` files that phase reads, plus `AGENTS.md`/`MEMORY.md`, `PRODUCT.md`/
`DESIGN.md` when product/UI is in scope, `git diff`/`git log` for review/wrap.

When the run is finished — plan checked off, review clean or patched, learnings routed, scratch
cleared — print the ✅ done card instead: one line on what shipped; one line on product-doc truth
(what was corrected, or none); recommended next (`workflow brainstorm <topic>` or `workflow
improve ...`; `memory.compact` if `MEMORY.md` has grown; open a PR if not committing to `main`).

## Ground rules (every phase)

- **Clarify before you build** (gate above). **Verify, don't trust** — check every interface,
  signature, column against real code; a generated spec is a hypothesis.
- **No backward-compat shims** unless asked; contract versions in lockstep producer→validator→consumer.
- **Handoff files live in `.workflow/`** (gitignored scratch); commit after each verified step.
- **Provenance header** — every `.workflow/` artifact opens with five lines written at creation:
  `Command:`, `Created:` (date), `Base:` (git sha), `Inputs:` (`<artifact> @ <its Base sha>` or `none`),
  `Status:` (`drafting` until the phase writes its closing section, then `complete`). At phase entry
  check your input's header holds — `Status: complete`, `Inputs` untouched since its `Created` (git
  log or mtime), `Base` still an ancestor of HEAD — else name the staleness and ask first.
- **Always close with the next-step card** (or ✅ done card).

## Command index

Read the listed reference before acting. `status` and `next` need only this file (plus
ROUTING.md for the card's seat mapping).

| Command | Reference |
|---|---|
| `workflow brainstorm`, `workflow improve` | `references/phase-0-brainstorm.md` |
| `workflow spec` | `references/phase-1-spec.md` |
| `workflow plan` | `references/phase-2-plan.md` |
| `workflow execute` | `references/phase-3-execute.md` |
| `workflow review` (+ patch cycle) | `references/phase-4-review.md` |
| `workflow wrap` | `references/wrap.md` |
| `workflow learn`, `workflow log` | `references/learning-worklog.md` |
| `workflow todo [idea]` | `references/todo.md` |
| `workflow bootstrap [PRD.md]` | `references/bootstrap.md` |

## Keeping this skill alive

When the workflow's shape genuinely changes, edit these files directly — they are the one source
of truth. **Growth rule:** a new feature or use case = a new (or extended) reference file plus
one Command-index line; this hub stays under ~205 lines. **Vendor rule:** `ROUTING.md` is the only file that names harnesses,
vendors, or models — a brand name anywhere in `SKILL.md` or `references/` is a bug (`README.md`
is reader-facing and exempt); seats and invariants live here, mappings live there. **Prompt-style rule (when
editing paste blocks and instructions):** state each rule exactly once (four sanctioned exceptions,
all defending paths a once-only statement can't reach: the ⚠️ head note defends the raw-read path;
the closing card imperative at every phase tail defends against tail-position card drops; the
Harness-verbs pointer may repeat wherever a file emits a literal harness command; and paste blocks
restate the provenance-header fields, since they reach sessions that never load this file) — a contradiction or
duplicate is worse than a missing detail; define the outcome, constraints, and completion bar
rather than prescribing every step; reserve ALWAYS/NEVER for true process invariants; don't
scatter "ask first"/"wait for approval" — the approval *principle* is stated once here, the
phase-by-phase *mapping* once in ROUTING.md, and nowhere else. Before
changing ROUTING.md, verify model names and effort levels against each harness's own picker and
current official model guide; never infer one harness's availability from another's.
