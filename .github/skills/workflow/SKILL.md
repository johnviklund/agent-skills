---
name: workflow
description: >
  Runs a personal five-phase solo-dev coding workflow across Codex CLI and Copilot CLI:
  brainstorm, spec, audit & plan, execute, review, then wrap-up (learning capture). Only trigger
  on an explicit, deliberate invocation of the form "workflow [phase] ..." or "/workflow [phase]
  ..." where phase is brainstorm, spec, plan, execute, review, learn, wrap, status, next, log,
  compact, or
  improve -- e.g. "workflow spec the retry mechanism", "/workflow execute", "workflow next", a
  bare "/workflow" (treated as next), "workflow log", "workflow compact", or "workflow improve
  [feature] - goal:
  [goal]". Do NOT trigger on casual mentions of spec, plan, execute, review, or learn
  anywhere else in a message -- this skill is intentionally narrow and explicit, never a broad
  natural-language matcher. Deliberately thin and single-voice -- no reviewer personas, no
  self-orchestrated sub-agents (GPT-5.6 ultra is permitted narrowly, on read-only seats only).
---

# Workflow

A personal five-phase workflow, roles matched to model strengths: one brainstorm partner, one
pragmatic generator/executor, one deep reviewer. Two CLIs stay in the loop on purpose — Codex and
Copilot draw from separate token/quota pools (more total throughput), and the split buys
cross-vendor independence: GPT-5.6 writes in Codex, Claude audits and reviews in Copilot.

**This file is the router.** Full instructions per command live in `references/` (paths relative
to this skill's directory) — read the one file for the invoked command before acting, per the
*Command index* below. Model/effort assignments live ONLY in the two tables here; reference files
point back rather than restating them.

## Invocation — deliberate, not ambient

Neither CLI supports user-defined slash commands (`/` is reserved for built-ins; skills trigger
via description matching), so there is no *true* `/workflow` command. The required form is a
message starting with `workflow` or `/workflow` plus a command word (see *Command index*). Try
`/workflow ...` first; if the CLI swallows the slash, drop it — both forms are identical. A
**bare** `workflow`/`/workflow` is treated as `workflow next`. **Do not** treat unprefixed
mentions of "spec", "plan", "execute", "review", or "learn" in normal conversation as an
invocation.

## Where are we? (re-grounding) — `workflow status`

Report which phase is next without acting, then wait for an explicit `workflow <phase>`. Check
`.workflow/` first — file presence/absence is the state machine, more trustworthy than memory:

- No files yet → Phase 0 (Brainstorm).
- `brainstorm.md` only → Phase 1 (Spec).
- `+ spec.md` → Phase 2 (Audit & Plan).
- `+ plan.md`, no "Deviations", checklist incomplete → Phase 3 (Execute).
- `plan.md` checklist complete → Phase 4 (Review).
- `+ patch_plan.md` → mid patch cycle (see `references/phase-4-review.md`).
- Everything present, plan checked off, review clean → wrap-up.

If a given `workflow <phase>` mismatches the file state (e.g. `execute` with no `plan.md`), say
so and ask before proceeding. Also read `AGENTS.md`, `MEMORY.md`, `PRODUCT.md`/`DESIGN.md` (if
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
want?". Skip the gate for mechanical, low-risk, clearly-specified work (Luna-tier edits,
`status`, `next`, `log`, `compact`). This complements the in-phase rules (Phase 0 dialogue,
Phase 3 stop-on-deviation); it doesn't replace them.

## Model routing — the single source of truth

Route across GPT-5.6 (Sol/Terra/Luna), Claude Sonnet 5, and Claude Opus 4.8 by task shape:

| Model | Workflow seat | Why |
|---|---|---|
| Claude Opus 4.8 | Plan audit, final review, patch plan | Highest accuracy on the hardest repo-level reasoning; the strict reviewer |
| GPT-5.6 Sol | Hardest execution: schema/SQL/contract edits, P0 fixes | Best long-horizon agentic/terminal execution; stays oriented across files, tests, follow-ups |
| GPT-5.6 Terra | Specs, everyday logic-bearing implementation, wrap (Codex) | Near-flagship quality at ~half Sol's price — the default execution lane |
| Claude Sonnet 5 | Brainstorm, wrap (Copilot), all-round Claude fallback | Near-Opus quality at Sonnet prices; strong root-cause tracing and CLI work |
| GPT-5.6 Luna | Mechanical transformations with an explicit expected result | Fastest/cheapest lane |

**Structural facts:** Codex CLI serves only OpenAI models (`gpt-5.6-sol/terra/luna`; `gpt-5.6` is
the family alias); Copilot CLI serves both vendors (use the ID its picker shows). Claude is only
reachable from Copilot — and that's a feature: a different vendor reviewing GPT-written code
catches blind spots a same-family sibling shares. Default CLI split: Phase 0/2/4 in Copilot
(Claude seats), Phase 1/3 in Codex (GPT seats). Every phase must still run in either CLI: staying
in Codex, use Terra for 0/1, Luna/Terra/Sol for 3, Sol for 2/4; staying in Copilot, use Sonnet 5
for 0/1/3 (effort scaled the way Luna→Terra→Sol would) and Opus 4.8 for 2/4. Never run the same
step in both CLIs.

**Availability:** open `/model` at session start; use a model only if listed (plan/policy/region/
rollout-dependent). Fallbacks: no Opus 4.8 → Sonnet 5 `xhigh` for Phase 2/4; no Sonnet 5 → Terra
for Phase 0; no GPT-5.6 in Codex → GPT-5.5 at the closest effort. Don't retry or silently switch
to `auto`.

## Effort & approval per step

Codex GPT-5.6 exposes `low/medium/high/xhigh/max`; Claude exposes up to `xhigh` — where a row
says `max`, use the highest level the picker lists. Use the lowest level that reliably works;
spend effort on what a reviewer would catch (signatures, schemas, contract versions), not rote
edits.

| Phase / work | Model | Effort | Approval |
|---|---|---|---|
| Phase 0 — brainstorm | Copilot Sonnet 5 (fallback: Terra) | medium | — (dialogue) |
| Phase 1 — spec | Codex Terra (fallback: GPT-5.5) | high | — (read-only) |
| Phase 2 — audit & plan | Copilot Opus 4.8 (fallback: Sonnet 5 `xhigh`) | high; xhigh hardest cases | — (read-only) |
| Phase 3 — mechanical edits | Codex Luna | low → medium | auto |
| Phase 3 — logic-bearing edits | Codex Terra | high | review each diff |
| Phase 3 — schema/SQL/contract | Codex Sol | xhigh | review each diff |
| Phase 4 — review | Copilot Opus 4.8 (fallback: Sonnet 5 `xhigh`) | max | — (read-only) |
| Patch plan (any severity) | Copilot Opus 4.8 (fallback: Sonnet 5) | high | — |
| Fix P0s | Codex Sol | high | review each diff |
| Fix P1/P2/P3s | Codex Terra | medium | auto |
| Final check & wrap-up | Copilot Sonnet 5 / Codex Terra | medium | auto |

Set effort in `/model`; Copilot also accepts `--reasoning-effort`, Codex `model_reasoning_effort`
in `~/.codex/config.toml`. Phase 3 in Copilot: mirror Luna/Terra/Sol, or Sonnet 5 with effort
scaled to the same shape — one approach per session.

**GPT-5.6 `ultra` — narrow, read-only exception.** Ultra parallelizes across Sol's internal
subagents: it buys *breadth*, not depth. Allowed only on a read-only seat in Codex (Phase 2/4, or
a hard read-only investigation) when the problem is genuinely wide — many files/subsystems
checkable independently. Never for anything that writes (Phase 3, fixes, wrap): parallel writers
break one-step-at-a-time and contract lockstep. Same output contract as the normal seat, and its
findings get the *verify, don't trust* treatment — confirm flagged signatures/versions against
real code. Ultra multiplies token burn; default to Sol `max` unless breadth is the bottleneck.

## Context hygiene

`/clear` at every phase handoff (0→1→2→3→4 and into wrap) — re-ground from `.workflow/*.md` + git
instead of dragging the previous phase's context (or the wrong model's mindset) forward; in
Copilot the reset precedes the Sonnet 5→Opus 4.8 swap. Mid-phase: Phase 3 persists state to
`.workflow/plan.md` after **every** step (checklist + `## Execution state` block), so any
compaction — manual or automatic — can never strand the run; when `/context` shows the window
filling, run `workflow compact` between steps (see `references/compact.md`) — don't hand-write a
`/compact` focus and don't let the CLI auto-compact decide what survives. After *any* compaction,
re-read the plan's `Execution state` block before the next step. Never compact at a
handoff or mid-verification-critical-step: summaries silently drop exact signatures and contract
versions.

## Next recommended step — `workflow next` (or bare `/workflow`)

Every phase response ends with this card; `workflow next` prints it on demand without doing phase
work (re-ground via *Where are we?* first). Print exactly this shape:

---
### ▶ Next recommended step

**You are here:** Phase N (\<name>) → **Next:** Phase M (\<name>)

| # | Step | Setting |
|---|------|---------|
| 1 | `/clear`? | Yes — reset before the handoff (or *No — continue this session*) |
| 2 | CLI · model · effort | from the tables above, with fallback |
| 3 | Ground in | exact files to read first |
| 4 | Invoke | the `workflow <phase>` command to send |

**GPT `/goal`:** yes/no (+why) · **GPT `/fast`:** yes/no (+why) · **Sub-agents:** none — except `ultra` per the read-only exception above

**Compact?** \<one of:> No — `/clear` at this handoff (never compact between phases) · No — window fine, just continue · Yes — mid-phase and `/context` is filling: run `workflow compact` between steps. Never hand-write `/compact`; the only `/compact` to run is the line `workflow compact` prints.

Paste next:
```text
<the exact prompt or `workflow <phase>` line to send>
```
---

Toggle defaults: `/clear` Yes at every handoff, No only for continued same-model work. **Compact**
by the rule on the card — the decision is: phase boundary → `/clear`, never compact; mid-phase +
window filling (check `/context`, roughly >70%) → `workflow compact` between steps, never
mid-step; otherwise nothing. Model/effort straight from the tables above. Ground in: the
`.workflow/*.md` files that phase reads, plus `AGENTS.md`/`MEMORY.md`, `PRODUCT.md`/`DESIGN.md`
when product/UI is in scope, `git diff`/`git log` for review/wrap. `/goal` default No (only
whole-plan Phase 3 in Codex, keeping review-each-diff on SQL/contract steps). `/fast` default No
(only mechanical auto-approve rows; never logic/schema or Phase 2/4). When the run is finished —
plan checked off, review clean or patched, learnings routed, scratch cleared — print the ✅ done
card instead: one line on what shipped; recommended next (`workflow brainstorm <topic>` or
`workflow improve ...`; `memory.compact` if `MEMORY.md` has grown; open a PR if not committing to
`main`).

## Ground rules (every phase)

- **Clarify before you build** (gate above). **Verify, don't trust** — check every interface,
  signature, column against real code; a generated spec is a hypothesis.
- **No backward-compat shims** unless asked; contract versions in lockstep producer→validator→consumer.
- **Handoff files live in `.workflow/`** (gitignored scratch); commit after each verified step.
- **Always close with the next-step card** (or ✅ done card).

## Command index

Read the listed reference before acting. `status` and `next` need only this file.

| Command | Reference |
|---|---|
| `workflow brainstorm`, `workflow improve` | `references/phase-0-brainstorm.md` |
| `workflow spec` | `references/phase-1-spec.md` |
| `workflow plan` | `references/phase-2-plan.md` |
| `workflow execute` | `references/phase-3-execute.md` |
| `workflow review` (+ patch cycle) | `references/phase-4-review.md` |
| `workflow wrap` | `references/wrap.md` |
| `workflow learn`, `workflow log` | `references/learning-worklog.md` |
| `workflow compact` | `references/compact.md` |

## Keeping this skill alive

When the workflow's shape genuinely changes, edit these files directly — they are the one source
of truth. **Growth rule:** a new feature or use case = a new (or extended) reference file plus
one Command-index line; this router stays under ~200 lines, and model/effort facts are edited
ONLY in the two tables above. Before changing model names or effort levels, verify Codex against
the current official model guide and Copilot with `/model` or `copilot help config`; never infer
one CLI's availability from the other's.
