---
name: workflow
description: >
  Trigger ONLY on a message of the form "workflow <command>" or "/workflow <command>" — a
  deliberate invocation of this personal five-phase solo-dev coding workflow; never on casual
  mentions of spec, plan, execute, review, or learn elsewhere in a message. Commands: brainstorm,
  improve, spec, plan, execute, review, learn, wrap, status, next, log, todo, bootstrap, realign;
  bare "workflow" = next.
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
command word (see *Command index*); most CLIs reserve `/` for built-ins, so if the CLI swallows
the slash, drop it — both forms are identical. A **bare** `workflow` is `workflow next`. **Do not**
treat unprefixed mentions of "spec", "plan", "execute", "review", or "learn" as an invocation.

## Where are we? (re-grounding) — `workflow status`

Report which phase is next without acting, then wait for an explicit `workflow <phase>`. The
`.workflow/` files are the state machine, more trustworthy than memory. Every artifact header
carries `Status:`, and **`drafting` means resume that phase, never advance past it**; standalone
`realign.md` is not phase state. Review is read-only, so `review.md` is its *only* evidence — never
infer "review done" without it. With every artifact `complete`:

| `.workflow/` | Next |
|---|---|
| empty | Phase 0 — Brainstorm |
| `brainstorm.md` | Phase 2 — Audit & Plan — or Phase 1 — Spec, only if the brainstorm's card called for it |
| `+ spec.md` | Phase 2 — Audit & Plan |
| `+ plan.md`, checklist incomplete | Phase 3 — Execute |
| checklist complete; no `review.md`, or its `Base` ≠ HEAD | Phase 4 — Review |
| `+ patch_plan.md` | mid patch cycle — `references/phase-4-review.md` |
| `review.md`, verdict clean or all findings dispositioned, `Base` = HEAD | wrap-up |

**Phase 1 is optional** — default route 0 → 2 → 3 → 4 → wrap. The brainstorm card recommends
`workflow spec` only for schema/SQL/contract-coupled work, subsystems the brainstorm could not size,
or an unfamiliar codebase. `workflow status` prints exactly three lines and stops: **Phase:** where
the state machine is; **Blocker:** what stops advancing, or `none`; **Next:** the line to send.

If a `workflow <phase>` mismatches the file state (e.g. `execute` with no `plan.md`), say so and
ask. Grounding also reads `ROUTING.md` (the seat and the mapping every card resolves), `AGENTS.md`,
`MEMORY.md`, `PRODUCT.md`/`DESIGN.md` (if product/UI is in scope), repo-root `TODO.md` (intake
scratchpad — Phase 0, Phase 2's TODO-impact check, wrap's TODO hygiene), `git log --oneline -15`, `git status`.

## Clarify before running a phase (every phase)

After re-grounding, before the first edit or line of output: **is anything material unclear, or
is a misunderstanding here expensive?** If yes, ask one round of max 2–3 questions shaped per
*Reporting* below, and wait; if no, act — this gate must not turn every phase into an interview.
Ask when: **ambiguous target/scope**; **command/state mismatch**; **high-cost-if-wrong work
ahead** (schema/SQL/contract, migrations, deletions — "I read this as X — correct?"); **a silent
assumption doing heavy lifting**; **an undecided input** — a phase never encodes an open question
as a checklist step or placeholder; it asks, then writes. Skip the gate for mechanical, low-risk,
clearly-specified work (`status`, `next`, `log`, `todo`, mechanical-lane edits). This complements
the in-phase rules (Phase 0 dialogue, Phase 3 stop-on-deviation); it doesn't replace them.

## Reporting to the human (every phase)

**The artifact is the record; chat is the receipt.** The human runs many projects at once and reads
chat on a phone; anything that is not a result or a decision goes in the `.workflow/` file, not the
turn. A phase's user-visible output above the card is at most ~12 lines:

- **Result** — what the phase did, ≤3 plain-language lines. No restating the plan, findings, or
  paste block; no narrating tool calls or file reads.
- **Decisions needed** — one numbered list, each item answerable with a letter: the question first,
  plain words, two sentences max; options on their own lines; a **recommended default** marked, so
  the list can be answered "all defaults except 2b". One decision per item; no term the human
  hasn't used unless defined in the same breath. This shape governs *every* question and escalation
  in *every* phase — clarify gate, ESCALATE items, review escalations, wrap contradictions.
- **A negative result is one line.** "`PRODUCT.md` — no changes." is complete; proving a negative
  with citations is noise. Evidence accompanies positive findings only.

**Artifact budgets** — thinking is unbounded, files are not: `brainstorm.md` ≤ ~40 lines; `spec.md`
≤ ~60; `plan.md` ≤ ~100 with ≤ 12 steps (more means the run is too big — propose a split and ask);
`review.md` grows with real findings only. Meet a budget by cutting prose, never checks.

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
  the code; a same-vendor review is a degraded mode — note it when unavoidable.
- **No double work.** Never run the same step in two sessions or two models.
- **Availability first.** Check the CLI's model picker at session start; walk ROUTING.md's
  fallback chain in order; never retry blindly or silently switch to an auto/default model.
- **Effort scales with risk, not habit.** Lowest level that reliably works: mechanical lowest,
  schema/contract and review highest (levels in ROUTING.md). Before *raising* effort on a
  struggling step, check whether it lacks a success criterion — a clearer completion bar beats
  more thinking. A new model baselines at the incumbent's effort and tests one level lower.
- **Approval scales with blast radius.** Read-only work needs none; mechanical-lane edits
  auto-approve; logic-bearing and schema/contract edits are reviewed diff by diff (the full
  phase→approval map is in ROUTING.md alongside the efforts).
- **Sub-agent/parallel modes are for read-only breadth only** — a read-only seat, many
  files/subsystems checkable *independently*, findings still *verified, not trusted*. Never on
  anything that writes: parallel writers break one-step-at-a-time and lockstep.

## Context hygiene

Reset the session at every phase handoff — re-ground from `.workflow/*.md` + git instead of
dragging the previous seat's context forward; the reset is where the model swap happens. Mid-phase,
reset too: every phase writes its artifact incrementally, so a reset costs warm cache only and,
unlike compaction, is safe at any fullness. This skill names only the verb — *reset*, *compact*,
*context meter*, *model picker*; the literal command is the CLI's own (`README.md` has a cheat sheet).

## Next recommended step — `workflow next` (or bare `/workflow`)

Every phase ends with this card as the turn's **last user-visible output** — **mandatory, no
substitute**: a conversational closer is not the card, and where a CLI ends the turn with a
completion/summary tool the card belongs *inside* that summary. Row 2 is **resolved from
`ROUTING.md` at print time** — concrete vendor, model, effort and first fallback; a seat name or
"see ROUTING.md" defeats the card's purpose, which is that the human never opens a file to learn
which model to pick. `workflow next` prints the card on demand without doing phase work. Exact shape:

---
### ▶ Next recommended step

**You are here:** Phase N (\<n>) → **Next:** Phase M (\<n>)

| # | Step | Setting |
|---|------|---------|
| 1 | Reset session? | Yes — reset before the handoff (or *No — continue this session*) |
| 2 | Vendor · model · effort | resolved values, e.g. "<vendor> · <model> · <effort> — fallback: <model> <effort>" |
| 3 | Ground in | exact files to read first |
| 4 | Invoke | the `workflow <phase>` command to send |

**Autonomy toggles:** none (default) — only where ROUTING.md sanctions one for this seat · **Sub-agents:** none — except the read-only breadth exception above
Paste next:
```text
<the exact prompt or `workflow <phase>` line to send>
```
---

Defaults: reset Yes at every handoff, No only for continued same-seat work; autonomy/speed
toggles No unless ROUTING.md sanctions them for that seat, never on review seats. Row 3 lists the `.workflow/*.md`
files that phase reads, plus `AGENTS.md`/`MEMORY.md`, `PRODUCT.md`/`DESIGN.md` when product/UI is
in scope, `git diff`/`git log` for review/wrap.

When the run is finished (plan checked off, review clean or patched, learnings routed, `.workflow/`
dispositioned) print the ✅ done card instead: one line on what shipped; one line on product-doc
truth (corrected what, or none); recommended next (`workflow brainstorm <next ROADMAP.md
initiative>` or `workflow improve ...`; `memory.compact` if `MEMORY.md` grew; PR if not on `main`).

## Ground rules (every phase)

- **Clarify before you build** (gate above). **Verify, don't trust** — check every interface,
  signature, column against real code; a generated spec is a hypothesis. **No backward-compat
  shims** unless asked; contract versions in lockstep producer→validator→consumer.
- **Handoff files live in `.workflow/`** — run scratch, **tracked in some repos, gitignored in
  others** (check; it decides if wrap's clean-up is recoverable); commit after each verified step.
- **Provenance header** — every `.workflow/` artifact opens with five lines: `Command:`, `Created:`
  (date), `Base:` (git sha), `Inputs:` (`<artifact> @ <its Base sha>` or `none`), `Status:`
  (`drafting` until the closing section is written, then `complete`). At phase entry check the
  input's header holds — `Status: complete`, `Inputs` untouched since its `Created`, `Base` an
  ancestor of HEAD — else name the staleness and ask first.
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
| `workflow wrap` | `references/wrap.md` |
| `workflow learn`, `workflow log` | `references/learning-worklog.md` |
| `workflow todo [idea]` | `references/todo.md` |
| `workflow bootstrap [PRD.md]` | `references/bootstrap.md` |
| `workflow realign` | `references/realign.md` |

## Keeping this skill alive

These files are the one source of truth — edit them directly when the workflow's shape changes.
**Growth rule:** a new feature = a new or extended reference file plus one Command-index line; this
hub stays under ~205 lines. **Vendor rule:** only `ROUTING.md` names vendors or models, and no
file in the skill names a CLI product — `README.md` is reader-facing and exempt from both. **Prompt-style rule:** state each rule exactly once —
four sanctioned exceptions defend paths a once-only statement can't reach: the ⚠️ head note (raw
reads), the closing-card imperative at every phase tail, and paste blocks restating the
provenance header. Define outcome,
constraints, and completion bar rather than every step; reserve ALWAYS/NEVER for true invariants;
the approval *principle* lives here, the *mapping* in ROUTING.md, nowhere else. Before changing
ROUTING.md, verify model names and efforts against the CLI's own picker.
