# ROUTING — current personal mapping (edit me)

This is the **only** file in the skill that names vendors or models. `SKILL.md` and `references/`
speak in seats; this file maps those seats onto *my current* models. Fork the skill, rewrite this
file for yours, and nothing else needs to change. (Last verified: 2026-09-11 — confirm picker IDs
for Opus 5 before trusting the reviewer row.)

**How a model earns a seat — trial runs.** Put the candidate in the seat for one or two real
runs; the worklog's `Run:` and `Seats:` lines (steps, review cycles, deviations, findings
overturned, model per seat) are the evidence, and `checkup` compares them against the
incumbent's last runs on that seat. Promote when the candidate ties or beats on cycles and
deviations at lower cost or effort; demote when it doesn't. The one exception is the strict
reviewer: before a candidate takes that seat, run `evals.run reviewer` — a recall check on ≤8
diffs with known P0/P1s — because a reviewer miss is the expensive kind. No other seat is examined.

**The CLI is not part of the mapping.** Some environments expose one CLI that serves both vendors;
others need one CLI per vendor. Either way this file is the same: pick the model, and use
whatever CLI serves it. The literal commands for *reset*, *compact*, *context meter* and *model
picker* are the CLI's own — `README.md` keeps a per-CLI cheat sheet.

## Vendors and models

| Vendor | Models in use | Effort scale |
|---|---|---|
| OpenAI | GPT-5.6 Sol / Terra / Luna (`gpt-5.6` = family alias); GPT-5.5 as fallback | `low / medium / high / xhigh / max` |
| Anthropic | Claude Opus 5, Claude Sonnet 5; Fable 5 where available; Haiku 4.5 | `low / medium / high / xhigh` — where a row says `max`, use the highest level the picker lists |

Vendor split: Anthropic fills the dialogue and judgement seats (Phase 0, 2, 4, wrap); OpenAI fills
the writing seats (Phase 1 when it runs, Phase 3). That is what makes review cross-vendor: GPT
writes, Claude audits and reviews. The invariant is the *split*, not the direction — reversed
works too.

## Seat mapping

| Seat | Primary (vendor · model · effort) | Fallback chain |
|---|---|---|
| Brainstorm partner | Anthropic · Sonnet 5 · medium | OpenAI · GPT-5.6 Terra · medium |
| Default executor | OpenAI · GPT-5.6 Terra · per phase table | GPT-5.5 · closest effort; then Anthropic · Sonnet 5 (breaks the vendor split — reviewer must then be OpenAI, degraded) |
| Heavy executor | OpenAI · GPT-5.6 Sol · xhigh (P0 fixes: high) | GPT-5.5 · xhigh; then Anthropic · Sonnet 5 · xhigh (same caveat) |
| Mechanical lane | OpenAI · GPT-5.6 Luna · low→medium | GPT-5.5 · low; then Anthropic · Sonnet 5 · low |
| Strict reviewer | Anthropic · Opus 5 · high (review: max) | Sonnet 5 · xhigh; then OpenAI · GPT-5.6 Sol (degraded: same-vendor review — note it in `review.md`) |

## Phase → seat · effort · approval

| Phase / work | Seat | Effort | Approval |
|---|---|---|---|
| Phase 0 — brainstorm | Brainstorm partner | medium | — (dialogue) |
| Phase 1 — spec (optional) | Default executor | xhigh | — (read-only) |
| Phase 2 — audit & plan | Strict reviewer | high; xhigh hardest cases | — (read-only) |
| Phase 3 — mechanical edits | Mechanical lane | low → medium | auto |
| Phase 3 — logic-bearing edits | Default executor | high | review each diff |
| Phase 3 — schema/SQL/contract | Heavy executor | xhigh | review each diff |
| Phase 4 — review | Strict reviewer | max | — (read-only) |
| Patch plan (any severity) | Strict reviewer | high | — |
| Fix P0s | Heavy executor | high | review each diff |
| Fix P1/P2/P3s | Default executor | medium | auto |
| Final check & wrap-up | Brainstorm partner | medium | auto |
| TODO intake (`workflow todo`) | Brainstorm partner | medium | auto (writes only `TODO.md`) |
| Bootstrap (`workflow bootstrap`) | Brainstorm partner (docs) + Strict reviewer (audit) | high | propose each doc, confirm before writing |
| Realign (`workflow realign`) | Strict reviewer | high | human approval per candidate before canonical-doc write |

**Single-vendor sessions** (only one vendor available today): OpenAI only — Terra for Phase 0/1,
Luna/Terra/Sol by step shape for Phase 3, Sol for Phase 2/4 (degraded same-vendor review). Anthropic
only — Sonnet 5 for Phase 0/1/3 (effort scaled the way the mechanical→default→heavy lanes would),
Opus 5 for Phase 2/4 (degraded same-vendor review). Either way, note the degradation in `review.md`.

## Model and mode notes

- **Read-only breadth.** Where the CLI serving a read-only seat offers a fan-out / sub-agent /
  "ultra"-style breadth mode, it is sanctioned only per `SKILL.md`'s read-only-breadth invariant
  (Phase 2/4, wide problems only). It multiplies token burn — default to the seat's normal effort
  unless breadth is the bottleneck.
- **Autonomy loops** (a mode that drives a whole plan without re-prompting each step) and **speed
  modes** (reduced reasoning): default No. An autonomy loop only when explicitly asked, and
  review-each-diff stays on schema/contract steps even inside it. A speed mode only on mechanical
  auto-approve rows, never logic/schema or Phase 2/4.
- **Wrap in practice:** wrap usually follows Phase 4 on the Anthropic side — drop Opus 5 → Sonnet 5
  in the same session after the review verdict.
- **Availability:** open the model picker at session start; models are plan/policy/region/rollout
  dependent. Missing Opus 5 → Sonnet 5 `xhigh` for the reviewer seat; missing Sonnet 5 → Terra for
  the brainstorm seat; missing GPT-5.6 → GPT-5.5 at the closest effort.
- **Fable 5 caveat:** Anthropic's own prompting guide warns that skills written for prior models
  are often too prescriptive for Fable 5 and can degrade output. If seating Fable as the reviewer,
  the outcome-shaped paste blocks here should hold up, but trim step-level prescription before
  trusting it — and run `evals.run reviewer` first, same as any reviewer candidate.
