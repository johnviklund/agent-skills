---
name: workflow
description: >
  Trigger ONLY on a message of the form "workflow <command>" or "/workflow <command>" — a
  deliberate invocation of this personal five-phase solo-dev coding workflow; never on casual
  mentions of spec, plan, execute, review, or learn elsewhere in a message. Commands: brainstorm,
  improve, spec, plan, execute, review, park, learn, wrap, status, next, log, todo, bootstrap,
  realign; most take an optional run slug; bare "workflow" = next.
---

# Workflow

> ⚠️ **This skill must be invoked, not skimmed.** A message of the form `workflow <command>` / `/workflow <command>` is a deliberate invocation: invoke this skill as the first action, before reading references, running commands, or emitting output. Reading `SKILL.md` or `references/*.md` directly is not a substitute for invoking the skill.

A personal five-phase workflow built on **seats** — jobs with an output contract and effort
profile, filled by whatever model currently earns them. Two model vendors stay in the loop on
purpose: splitting writer from reviewer across vendors buys independence. Which CLI serves the
models is not the skill's concern — one CLI may serve both vendors, or each may need its own. **This file is the hub, and it is vendor-neutral.** Full instructions per command
live in `references/` (read the one file for the invoked command, per the *Command index*). Seats
map to CLIs, models, efforts, and fallbacks in **`ROUTING.md`** — the only file that names vendors,
and the only one a fork edits.

**Invocation is deliberate, not ambient:** a message starting with `workflow` or `/workflow` plus a
command word and, for phase commands, an optional run slug (`workflow plan auth-refresh`); most
CLIs reserve `/` for built-ins, so if the CLI swallows the slash, drop it. A **bare** `workflow` is
`workflow next`. **Do not** treat unprefixed mentions of "spec", "plan", "execute", "review", or
"learn" as an invocation.

## Runs and where we are — `workflow status`

**One run = one folder, `.workflow/<slug>/`**, created by `workflow brainstorm <slug>`, tracked in
git, kept forever. A folder is self-describing — any fresh context can pick a run up from its files
alone — so the run files are the state machine. Every artifact header carries `Status:` (`drafting` ·
`complete` · `parked` · `done`); **`drafting` means resume that phase, never advance past it**; a run
whose `brainstorm.md` is `parked` or `done` is not live. `review.md` is review's *only* evidence. For a live run:

| in `.workflow/<slug>/` | Next |
|---|---|
| `brainstorm.md` only | Phase 2 — Audit & Plan — or Phase 1 — Spec, only if the brainstorm's card called for it |
| `+ spec.md` | Phase 2 — Audit & Plan |
| `+ plan.md`, checklist incomplete | Phase 3 — Execute |
| checklist complete; no `review.md`, or code changed since its `Base` | Phase 4 — Review |
| `+ patch_plan.md` with unchecked steps | Phase 3 — Execute (runs the patch plan, not the plan) |
| `patch_plan.md` complete; code changed since `review.md`'s `Base` | Phase 4 — Review, cycle N+1 |
| `review.md` with no open finding and no code change since its `Base` | wrap-up (resumes `wrap.md` if present) |

**Phase 1 is optional** — default route 0 → 2 → 3 → 4 → wrap. **Resolving the slug:** a phase
command names one, or there is exactly one live run — else list the live runs and ask, lettered.
Grounding reads only live runs (`grep -l 'Status: \(drafting\|complete\)' .workflow/*/brainstorm.md`),
never done or parked folders. `workflow status` prints one line per non-done run — `slug · phase ·
blocker or none · next command` — then stops.

If a `workflow <phase>` mismatches the run's state (e.g. `execute` with no `plan.md`), say so and
ask. Grounding also reads `ROUTING.md`, `AGENTS.md`, `MEMORY.md` (the index of `memory/`),
`PRODUCT.md`/`DESIGN.md` (if product/UI is in scope), repo-root `TODO.md` (ideas not yet
brainstormed), `git log --oneline -15`, `git status`.

## Clarify before running a phase (every phase)

After re-grounding, before the first edit or line of output: **is anything material unclear, or
is a misunderstanding here expensive?** If yes, ask one round of max 2–3 questions shaped per
*Reporting*, and wait; if no, act — the gate must not turn every phase into an interview. Ask
when: **ambiguous target/scope**; **command/state mismatch**; **high-cost-if-wrong work ahead**
(schema/SQL/contract, migrations, deletions — "I read this as X — correct?"); **a silent assumption
doing heavy lifting**; **an undecided input** — never encoded as a checklist step or placeholder.
Skip it for mechanical, clearly-specified work (`status`, `next`, `log`, `todo`, mechanical edits).

## Reporting to the human (every phase)

**The artifact is the record; chat is the receipt.** The human runs many projects at once and reads
chat on a phone; anything that is not a result or a decision goes in the run's file, not the
turn. A phase's user-visible output above the card is at most ~12 lines:

- **Result** — what the phase did, ≤3 plain-language lines; no restating plan, findings, or paste
  block, no narrating tool calls.
- **Decisions needed** — one numbered list, each item answerable with a letter: question first,
  plain words, two sentences max; options on their own lines; a **recommended default** marked, so
  the list can be answered "all defaults except 2b". One decision per item; no undefined jargon.
  This shape governs *every* question and escalation in *every* phase.
- **A negative result is one line** ("`PRODUCT.md` — no changes."); evidence accompanies positives only.

**Artifact budgets** — thinking is unbounded, files are not: `brainstorm.md` ≤ ~40 lines; `spec.md`
≤ ~60; `plan.md` ≤ ~100 with ≤ 12 steps (more = split the run and ask); `review.md` grows with real
findings only. Meet a budget by cutting prose, never checks.

## Seats — roles, not vendors

| Seat | Job | Used by |
|---|---|---|
| Brainstorm partner | Knowledge-shaped dialogue, intake; cheap, conversational | Phase 0, `workflow todo`, wrap |
| Default executor | Specs and everyday logic-bearing implementation; best quality-per-cost | Phase 1 (optional), Phase 3 (logic), P1–P3 fixes, wrap |
| Heavy executor | Schema/SQL/contract-coupled edits, hardest multi-file work; best long-horizon agentic model | Phase 3 (schema/contract), P0 fixes |
| Mechanical lane | Transforms with an explicit expected result; cheapest/fastest | Phase 3 (mechanical) |
| Strict reviewer | Deepest reasoning; the workflow's skeptic — read-only except `realign` | Phase 2, Phase 4, patch plans; `realign` (canonical-doc writes, per-candidate human approval) |

Which vendor and model fill each seat, at what effort, with what fallback — that's `ROUTING.md`;
a model earns a seat on trial runs, not exams (protocol there). **Portable invariants:**

- **Cross-vendor review.** The strict reviewer is a different vendor from whichever model wrote
  the code; same-vendor review is a degraded mode — note it. **No double work:** never run the
  same step in two sessions or two models.
- **Availability first.** Check the CLI's model picker at session start; walk ROUTING.md's
  fallback chain in order; never retry blindly or silently switch to an auto/default model.
- **Effort scales with risk, not habit** (levels in ROUTING.md). Before *raising* effort on a
  struggling step, check whether it lacks a success criterion — a clearer completion bar beats
  more thinking. A new model baselines at the incumbent's effort and tests one level lower.
- **Approval scales with blast radius.** Read-only none; mechanical auto; logic-bearing and
  schema/contract edits reviewed diff by diff (phase→approval map in ROUTING.md).
- **Sub-agent/parallel modes are for read-only breadth only** — a read-only seat, independently
  checkable files, findings still *verified, not trusted*. Never on anything that writes.

## Context hygiene

Every phase runs in a fresh context — today a reset session, later a delegated agent — re-grounded
from `.workflow/<slug>/` + git, never from the previous seat's context; the handoff is where the
model swap happens. Mid-phase, reset too: artifacts are written incrementally, so a reset costs warm
cache only and, unlike compaction, is safe at any fullness. This skill names only the verb — *reset*,
*compact*, *context meter*, *model picker*; the literal command is the CLI's own (cheat sheet in `README.md`).

## Next recommended step — `workflow next` (or bare `/workflow`)

Every phase ends with this card as the turn's **last user-visible output** — **mandatory, no
substitute**: a closer sentence is not the card; where a CLI ends the turn with a summary tool the
card goes *inside* it. Row 2 is **resolved from `ROUTING.md` at print time** — open the file in the
closing turn and copy the next seat's vendor, model, effort, context window and first fallback
(a *Trial* entry prints instead of the primary, marked so); a seat name or "see ROUTING.md" is a
defect: the card exists so the human never opens a file to pick a model. `workflow next` prints it on demand. Exact shape:

---
### ▶ Next recommended step

**You are here:** Phase N (\<n>) → **Next:** Phase M (\<n>)

| # | Step | Setting |
|---|------|---------|
| 1 | Reset session? | Yes — reset before the handoff (or *No — continue this session*) |
| 2 | Vendor · model · effort · context | resolved values, e.g. "<vendor> · <model> · <effort> · <context window> — fallback: <model> <effort>" |
| 3 | Ground in | exact files to read first |
| 4 | Invoke | the `workflow <phase>` command to send |

**Autonomy toggles:** none (default) — only where ROUTING.md sanctions one for this seat · **Sub-agents:** none — except the read-only breadth exception above
Paste next:
```text
<the exact prompt or `workflow <phase>` line to send>
```
---

Defaults: reset Yes at every handoff, No only for continued same-seat work; autonomy/speed
toggles No unless ROUTING.md sanctions them for that seat, never on review seats. Row 3 lists the
`.workflow/<slug>/` files that phase reads, plus `AGENTS.md`/`MEMORY.md`, `PRODUCT.md`/`DESIGN.md`
when product/UI is in scope, `git diff`/`git log` for review/wrap.

When the run is finished (plan checked off, review clean or patched, learnings routed, folder
archived) print the ✅ done card instead: one line on what shipped; one line on product-doc
truth (corrected what, or none); recommended next (`workflow brainstorm <next ROADMAP.md
initiative>` or `workflow improve ...`; `memory.compact` if `MEMORY.md` grew; PR if not on `main`).

## Ground rules (every phase)

- **Clarify before you build** (gate above). **Verify, don't trust** — check every interface,
  signature, column against real code; a generated spec is a hypothesis. **No backward-compat
  shims** unless asked; contract versions in lockstep producer→validator→consumer.
- **Run files live in `.workflow/<slug>/` and are tracked** — commit after each verified step.
  Wrap archives a run (`Status: done`, transient files dropped); it never deletes a folder.
- **Provenance header** — every `.workflow/` artifact opens with five lines: `Command:`, `Created:`
  (date), `Base:` (git sha), `Inputs:` (`<artifact> @ <its Base sha>` or `none`), `Status:`
  (`drafting` until the closing section is written, then `complete`). At phase entry check the
  input's header holds — `Status: complete`, `Inputs` untouched since its `Created` — and that its
  **relevant code is fresh**: `git diff --stat <Base>..HEAD -- <every file the artifact names>` is
  empty apart from this run's own step commits. Ancestry alone proves nothing; else name the
  staleness and route to the phase that must rerun (a stale plan → `workflow plan <slug>`).
- **Receipt files vs code.** `*.md` and `*.txt` are receipts; everything else — any script,
  config, or data file, in *any* directory including `.workflow/` and `docs/` — is code.
  "Code changed since sha X" means `git diff --stat X..HEAD -- . ':(exclude)*.md' ':(exclude)*.txt'`
  is non-empty; receipt commits never stale a review, and code never ships unreviewed because of
  the folder it sits in. Nothing outside `.workflow/` may depend on anything inside it.
- **Report per *Reporting*; always close with the next-step card** (or ✅ done card).

## Command index

Read the listed reference before acting; `status` and `next` need only this file plus ROUTING.md.

| Command | Reference |
|---|---|
| `workflow brainstorm`, `workflow improve` | `references/phase-0-brainstorm.md` |
| `workflow spec` (optional) | `references/phase-1-spec.md` |
| `workflow plan` | `references/phase-2-plan.md` |
| `workflow execute` | `references/phase-3-execute.md` |
| `workflow review` (+ patch cycle) | `references/phase-4-review.md` |
| `workflow park [slug]` | `references/phase-0-brainstorm.md` (parking section) |
| `workflow wrap` | `references/wrap.md` |
| `workflow learn`, `workflow log` | `references/learning-worklog.md` |
| `workflow todo [idea]` | `references/todo.md` |
| `workflow bootstrap [PRD.md]` | `references/bootstrap.md` |
| `workflow realign` | `references/realign.md` |

## Keeping this skill alive

**Growth rule:** a new feature = a new or extended reference file plus one Command-index line;
this hub stays under ~205 lines. **Vendor rule:** only `ROUTING.md` names vendors or models, and no
file in the skill names a CLI product — `README.md` is reader-facing and exempt from both. **Prompt-style rule:** state each rule exactly once —
four sanctioned exceptions defend paths a once-only statement can't reach: the ⚠️ head note (raw
reads), the closing-card imperative at every phase tail, and paste blocks restating the
provenance header. Define outcome,
constraints, and completion bar rather than every step; reserve ALWAYS/NEVER for true invariants;
the approval *principle* lives here, the *mapping* in ROUTING.md, nowhere else. Before changing
ROUTING.md, verify model names and efforts against the CLI's own picker.
