# ROUTING — current personal mapping (edit me)

This is the **only** file in the skill that names vendors, harnesses, or models. `SKILL.md` and
`references/` speak in seats; this file maps those seats onto *my current* tools. Fork the skill,
rewrite this file for yours, and nothing else needs to change. A model earns or loses a seat here
via `evals.run` scorecards — routing edits are deliberate, not vibes. (Last verified: 2026-07-16.)

## Harnesses

| Harness | Serves | Notes |
|---|---|---|
| Codex CLI | OpenAI models only (`gpt-5.6-sol/terra/luna`; `gpt-5.6` = family alias) | Effort also settable via `model_reasoning_effort` in `~/.codex/config.toml` |
| Copilot CLI | Both vendors (GPT-5.6 tiers, Claude Sonnet 5, Claude Opus 4.8 — use the picker's IDs) | Effort also settable via `--reasoning-effort` |
| Claude Code | Anthropic models only (Sonnet 5, Opus 4.8, Fable 5, Haiku 4.5) | **Not in my active mapping** (blocked in my environment) — supported harness for forks; example mapping below |

Default harness split: Phase 0/2/4 in Copilot (Claude seats), Phase 1/3 in Codex (GPT seats) —
separate quota pools + cross-vendor review (GPT-5.6 writes, Claude audits/reviews). Every phase
must still run in either harness; single-harness equivalents are listed below.

## Harness verbs

`SKILL.md` and `references/` name only the verb; the literal command lives here. Verify each cell
against the harness's own `/` menu — these drift.

| Harness | Reset session | Compact | Context meter | Model picker | Explicit skill invocation |
|---|---|---|---|---|---|
| Codex CLI | `/new` (`/clear` also starts a fresh chat) | `/compact` | `/status` (context usage + rate limits) | `/model` | `$workflow` |
| Copilot CLI | `/clear` | `/compact` | `/context` (tokens used, buffer left) | `/model` | none for project skills — description-matched; `/skills list\|info\|reload` inspects, and a plugin-installed skill becomes `/<plugin>:workflow` |
| Claude Code | `/clear` | `/compact` | `/context` | `/model` | `/workflow` (the skill's directory name is the command) |

Copilot CLI auto-compacts at roughly 80% of the window, which is why `references/compact.md` puts
the manual threshold at ~70% — below the automatic trigger, with room to reconcile first.

## Seat mapping

| Seat | Primary (harness · model · effort) | Fallback chain |
|---|---|---|
| Brainstorm partner | Copilot · Claude Sonnet 5 · medium | Copilot/Codex · GPT-5.6 Terra · medium |
| Default executor | Codex · GPT-5.6 Terra · per phase table | GPT-5.5 · closest effort; in Copilot: Sonnet 5 |
| Heavy executor | Codex · GPT-5.6 Sol · xhigh (P0 fixes: high) | GPT-5.5 · xhigh; in Copilot: Sonnet 5 · xhigh |
| Mechanical lane | Codex · GPT-5.6 Luna · low→medium | GPT-5.5 · low; in Copilot: Sonnet 5 · low |
| Strict reviewer | Copilot · Claude Opus 4.8 · high (review: max) | Sonnet 5 · xhigh; in Codex: GPT-5.6 Sol (degraded: same-vendor review — note it) |

Effort scales: Codex GPT-5.6 exposes `low/medium/high/xhigh/max`; Claude models expose up to
`xhigh` — where a row says `max`, use the highest level the picker actually lists.

## Phase → seat · effort · approval

| Phase / work | Seat | Effort | Approval |
|---|---|---|---|
| Phase 0 — brainstorm | Brainstorm partner | medium | — (dialogue) |
| Phase 1 — spec | Default executor | high | — (read-only) |
| Phase 2 — audit & plan | Strict reviewer | high; xhigh hardest cases | — (read-only) |
| Phase 3 — mechanical edits | Mechanical lane | low → medium | auto |
| Phase 3 — logic-bearing edits | Default executor | high | review each diff |
| Phase 3 — schema/SQL/contract | Heavy executor | xhigh | review each diff |
| Phase 4 — review | Strict reviewer | max | — (read-only) |
| Patch plan (any severity) | Strict reviewer | high | — |
| Fix P0s | Heavy executor | high | review each diff |
| Fix P1/P2/P3s | Default executor | medium | auto |
| Final check & wrap-up | Brainstorm partner (Copilot) / Default executor (Codex) | medium | auto |
| TODO intake (`workflow todo`) | Brainstorm partner | medium | auto (writes only `TODO.md`) |
| Bootstrap (`workflow bootstrap`) | Brainstorm partner (docs) + Strict reviewer (audit) | high | propose each doc, confirm before writing |

Single-harness sessions: staying in Codex — Terra for Phase 0/1, Luna/Terra/Sol by step shape for
Phase 3, Sol for Phase 2/4 (degraded same-vendor review). Staying in Copilot — Sonnet 5 for
Phase 0/1/3 (effort scaled the way the mechanical→default→heavy lanes would) and Opus 4.8 for
Phase 2/4.

## Harness-specific notes

- **GPT-5.6 `ultra`** (Codex) is this mapping's read-only breadth mode — Sol fanning out to
  internal subagents. Sanctioned only per SKILL.md's read-only-breadth invariant (Phase 2/4 in
  Codex, wide problems only); it multiplies token burn, so default to Sol `max` unless breadth is
  the bottleneck.
- **Codex `/goal`** can drive a whole Phase 3 plan end to end without re-prompting: default No;
  only when explicitly asked, and keep review-each-diff on schema/contract steps even under a
  goal. **Codex `/fast`**: default No; only mechanical auto-approve rows, never logic/schema or
  Phase 2/4.
- **Wrap in practice:** wrap usually follows Phase 4 in Copilot — drop Opus 4.8 → Sonnet 5 in
  the same session after the review verdict.
- **Availability:** open `/model` at session start; models are plan/policy/region/rollout
  dependent. If Copilot lacks Opus 4.8 → Sonnet 5 `xhigh` for the reviewer seat; if it lacks
  Sonnet 5 → Terra for the brainstorm seat; if Codex lacks GPT-5.6 → GPT-5.5 at the closest
  effort.

## Example fork mapping: Claude Code + Codex

For forks whose environment allows Claude Code, the natural cross-vendor pairing (untested by
me — validate on your first run):

- **Skill install:** Claude Code loads skills from `.claude/skills/` (project) or
  `~/.claude/skills/` (personal) — copy or symlink this skill folder there; the
  `workflow <command>` invocation grammar is unchanged.
- **Seat mapping:** Codex fills the writer seats (heavy executor: Sol; default executor: Terra;
  mechanical lane: Luna); Claude Code fills brainstorm partner (Sonnet 5 `medium`) and strict
  reviewer (Opus 4.8, or Fable 5 where available), preserving cross-vendor review. Reverse
  works too (Claude writes, a GPT harness reviews) — the invariant is the *split*, not the
  direction.
- **Efforts:** Claude Code exposes `low/medium/high/xhigh`; its session mode `/effort ultracode`
  (xhigh + automatic workflow orchestration) and the Task/sub-agent tool are this harness's
  parallel-breadth modes — read-only seats only, per SKILL.md's read-only-breadth invariant.
- **Fable 5 caveat:** Anthropic's own prompting guide warns that skills written for prior models
  are often too prescriptive for Fable 5 and can degrade output. If seating Fable as the
  reviewer, the outcome-shaped paste blocks here should hold up, but trim step-level
  prescription before trusting it — and run `evals.run reviewer` before giving it the seat,
  same as any candidate.
- **Single-harness Claude Code** is possible (Sonnet 5 for brainstorm/execution lanes scaled by
  effort, Opus 4.8/Fable 5 for Phase 2/4) but review becomes same-vendor — a degraded mode; add
  a second harness from another vendor to restore the invariant.
