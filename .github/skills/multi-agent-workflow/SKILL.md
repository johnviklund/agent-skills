---
name: multi-agent-workflow
description: >
  Runs a personal, five-phase solo-dev coding workflow across Codex CLI and Copilot CLI:
  brainstorm, spec, audit & plan, execute, review, then wrap-up. Use whenever the user wants to
  start or continue this workflow — phrases like "let's brainstorm", "spec this", "write the
  spec", "audit and plan this", "audit the spec", "execute the plan", "run phase 3", "review
  this", "review the changes", "wrap up", or "curate learnings" should trigger it, even without
  the word "skill" or "workflow". Also use it to re-ground mid-workflow (e.g. "where are we in
  the plan?") by reading .workflow/*.md and git state. This is a deliberately thin, single-voice
  workflow — no reviewer personas, no sub-agent orchestration — built to replace ad-hoc
  copy-pasted prompts, not to imitate heavier multi-agent review systems.
---

# Multi-Agent Workflow

A personal five-phase workflow, roles matched to model strengths: one brainstorm partner, one
fast generator/executor, one deep reviewer. Two CLIs stay in the loop on purpose — Codex and
Copilot draw from separate token/quota pools, so splitting phases across both buys more total
throughput than picking one tool and living inside its limits. Roles matter more than the brand;
swap models freely as better ones become available.

| Tool | Model | Role |
|---|---|---|
| Copilot CLI | Claude Sonnet 5 | Brainstorm/ideation partner (Phase 0); capable alternative executor for Phase 3 if already in this CLI |
| Codex CLI | GPT-5.5 | Fast generation + autonomous execution — default for Phase 1 and Phase 3 |
| Copilot CLI | Claude Opus 4.8 | Deep reasoning, architecture critique, strict review (Phase 2 & 4) |

Phase 3 (execute) is the one phase with a real choice: default to Codex/GPT-5.5, but Sonnet 5 in
Copilot is an equally capable stand-in if already there and not wanting to switch tools. Never run
the same step in both — pick one per plan (or split the plan by file to parallelize across both).

## Where are we? (re-grounding)

This skill can be invoked at the start of a fresh session or mid-workflow. Before doing anything,
check `.workflow/` for `brainstorm.md`, `spec.md`, `plan.md`, `patch_plan.md`, and `learnings.md`
— their presence/absence is the state machine, more trustworthy than a remembered summary:

- No files yet → Phase 0 (Brainstorm).
- `brainstorm.md` only → Phase 1 (Spec).
- `+ spec.md` → Phase 2 (Audit & Plan).
- `+ plan.md`, no "Deviations" section, checklist incomplete → Phase 3 (Execute).
- `plan.md` checklist complete → Phase 4 (Review).
- `+ patch_plan.md` → mid patch cycle, see *After Phase 4*.
- Everything present and the plan is fully checked off with a clean review → *Final check &
  wrap-up*.

If genuinely ambiguous, ask which phase to run rather than guessing. Also read `AGENTS.md`,
`MEMORY.md`, `PRODUCT.md`/`DESIGN.md` (if this touches product direction or UI), and
`git log --oneline -15` / `git status` as part of grounding — the same re-ground that used to be a
copy-pasted block is now just: run this skill.

## Reasoning effort & approval per step

Available levels — GPT-5.5: `low / medium / high / xhigh`; Sonnet 5 and Opus 4.8 both add `max`.
**Lower effort raises throughput; higher effort lowers hallucination.** Spend it on anything a
reviewer would catch (signatures, schemas, lockstep contract versions); save it on rote edits.

| Phase / work | Model | Effort | Approval |
|---|---|---|---|
| Phase 0 — brainstorm | Copilot Sonnet 5 | medium | — (interactive dialogue) |
| Phase 1 — spec | Codex GPT-5.5 | high | — (read-only) |
| Phase 2 — audit & plan | Copilot Opus 4.8 | high → max (scale with size/risk) | — (read-only) |
| Phase 3 — mechanical edits | Codex GPT-5.5 (or Copilot Sonnet 5) | medium | auto |
| Phase 3 — logic-bearing edits | Codex GPT-5.5 (or Copilot Sonnet 5) | high | review each diff |
| Phase 3 — schema / SQL / contract-coupled | Codex GPT-5.5 (or Copilot Sonnet 5) | xhigh | review each diff |
| Phase 4 — review | Copilot Opus 4.8 | max | — (read-only) |
| Patch plan (any severity) | Copilot Opus 4.8 | high | — |
| Fix P0s | Codex GPT-5.5 (or Copilot Sonnet 5) | high | review each diff |
| Fix P1/P2/P3s | Codex GPT-5.5 (or Copilot Sonnet 5) | medium | auto |

Set effort in `/model` (Codex also via `model_reasoning_effort` in `~/.codex/config.toml`).

## Context hygiene — `/clear` vs `/compact`

- **`/clear` between phases** (0→1→2→3→4), always — re-ground from `.workflow/*.md` + git
  instead of dragging the previous phase's context (or the wrong model's mindset) forward. Copilot
  carries Phase 0 into Phase 2/4 (switch Sonnet→Opus with `/model` at that handoff — it's both a
  context reset and a model swap); Codex carries Phase 1 into Phase 3 (no model swap needed).
- **`/compact [focus]` mid-phase only** — when one long Execute/Review session is filling up
  (check `/context`) but still needs to remember what it just did. Always give focus, e.g.
  `/compact keep plan step status, baseline test results, and the current diff; drop old file
  dumps`.
- **Never `/compact` at a handoff or for verification-critical work** — a summary can silently
  drop exact signatures, line numbers, or contract versions. `/clear` and re-ground instead.

## Ground rules (every phase)

- **Verify, don't trust.** Check every interface, signature, and column against the real
  code/schema — a generated spec is a hypothesis, not a fact.
- **No backward-compat shims** unless asked. Keep contract versions in lockstep across producer
  → validator → consumer.
- **Handoff files live in `.workflow/`** (gitignored scratch; only durable docs/skills get
  committed) so both CLIs can share state without polluting `docs/` or git.
- **Commit after each verified step.**

## Learning loop

Commits save *what* changed; `MEMORY.md`, skills, and `DESIGN.md` save *why*. This is the point of
the workflow, not an afterthought: **solve a real problem → remember it. Do the same kind of thing
3+ times → turn it into a skill.**

Append one line per learning to `.workflow/learnings.md` before any `/clear` or `/compact`, and any
time something worth keeping gets solved — don't wait for wrap-up:

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
own. For periodic `MEMORY.md` cleanup, invoke `memory.compact` manually — it never runs on its own.

## Phase 0 — Brainstorm (Copilot, Sonnet 5, medium)

If already in Copilot on Sonnet 5: read `PRODUCT.md`/`DESIGN.md` if they exist and this touches
product direction or UI, then run the brainstorm directly — ask clarifying questions one at a
time (prioritize whichever answer would change architecture/scope the most), explore 2-3
approaches with tradeoffs, push back on shaky assumptions or oversized scope. Don't write a spec
yet. When converged, save a short summary (problem statement, chosen approach, explicit
non-goals, open questions) to `.workflow/brainstorm.md`.

If handing this to a fresh Copilot session, paste:

```text
Read PRODUCT.md and DESIGN.md first, if they exist and this touches product direction or UI, so we don't relitigate settled decisions. Let's brainstorm before we spec anything: [describe the idea, problem, or need]. Ask me clarifying questions one at a time, prioritizing whichever question's answer would change the architecture or scope the most. Explore 2-3 different approaches with tradeoffs, and push back on any assumption that seems shaky or any scope that seems bigger than the actual need. Don't write a spec yet. When we've converged, save a short summary (problem statement, chosen approach, explicit non-goals, open questions) to .workflow/brainstorm.md.
```

**Variants:**
- **Blind spot pass** (unfamiliar territory, don't know what to ask yet): find unknown unknowns —
  what would an expert here know that isn't known — and explain them before brainstorming
  approaches.
- **Reference instead of prose** (can't describe what's wanted but would recognize it): read the
  named file/library/component as the reference for shape/behavior, then brainstorm how it adapts
  here.

## Phase 1 — Spec (Codex, high)

If already in Codex: read `AGENTS.md` + `MEMORY.md` + `PRODUCT.md`/`DESIGN.md` (if relevant) +
`.workflow/brainstorm.md` (if present) + the code. Propose the architecture, modified interfaces,
and every impacted file. Verify each existing signature against the actual code and flag anything
only inferred. Save to `.workflow/spec.md`.

If handing this to a fresh Codex session, paste:

```text
Read AGENTS.md + MEMORY.md + PRODUCT.md/DESIGN.md (if this touches product direction or UI) + .workflow/brainstorm.md (if present) + the code. We're refactoring [feature]. Propose the architecture, the modified interfaces, and every impacted file. Verify each existing signature against the actual code and flag anything you're only inferring. Save to .workflow/spec.md.
```

## Phase 2 — Audit & Plan (Copilot, Opus 4.8, high → max)

If already in Copilot: switch to Opus 4.8 with `/model` if still on Sonnet, then read
`.workflow/spec.md`, the repo, and `PRODUCT.md`/`DESIGN.md` if relevant (flag conflicts). Find
architectural blind spots, circular dependencies, and hallucinated signatures — confirm against
the real code. Rewrite as a sequential, file-by-file checklist, core interfaces before consumers,
each step with a verification check. Lead with whatever's most likely to need a human tweak (data
model/schema shape, type interfaces, user-facing behavior); put mechanical steps at the bottom.
Save to `.workflow/plan.md`.

If handing this to a fresh Copilot session on Opus 4.8, paste:

```text
Read .workflow/spec.md, the repo, and PRODUCT.md/DESIGN.md if this touches product or UI (flag anything that conflicts with either). Find architectural blind spots, circular dependencies, and hallucinated signatures — confirm against the real code. Then rewrite it as a sequential, file-by-file checklist, core interfaces before consumers, each step with a verification check. Lead the checklist with whatever's most likely to need a human tweak (data model/schema shape, type interfaces, user-facing behavior) — put mechanical/rote steps at the bottom. Save to .workflow/plan.md.
```

## Phase 3 — Execute (Codex GPT-5.5, or Copilot Sonnet 5 — medium; high for logic, xhigh for SQL/contracts)

Default to Codex; use Copilot/Sonnet 5 only if already there and not wanting to switch tools —
don't run the same step in both.

If already in the right tool: read `.workflow/plan.md`, capture the baseline test state (which
failures are pre-existing/environmental), then work ONE step at a time — edit, run checks, show
the diff, commit. If an edge case forces a deviation, take the conservative option, note it under
a "Deviations" section in `.workflow/plan.md`, and keep going — don't silently improvise. Stop on
any failed check or unresolved file. Keep contract versions in lockstep; no compat shims. Append
this phase's learnings as they happen (see *Learning loop*) rather than only at the end.

If handing this to a fresh session, paste:

```text
Read .workflow/plan.md. First capture the baseline test state (which failures are pre-existing or environmental). Then work ONE step at a time: edit, run checks, show the diff, commit. If an edge case forces a deviation from the plan, take the conservative option, note it under a "Deviations" section in .workflow/plan.md, and keep going — don't silently improvise. Stop on any failed check or unresolved file. Keep contract versions in lockstep; no compat shims. Use the plan as a mutable tracker.
```

Codex-only optional autonomous loop: `/goal` can drive the whole plan end to end without
re-prompting each step — worth knowing about, but not the default here; only reach for it if
explicitly asked, and keep `review each diff` on SQL/contract steps even under a goal.

## Phase 4 — Review (Copilot, Opus 4.8, max)

If already in Copilot on Opus: review the changes against `.workflow/plan.md` as a strict senior
engineer, including any "Deviations" logged during execution. Verify empirically — compile, run
tests, trace producer↔consumer, run live queries. Flag: missing error handling, resource leaks,
security flaws, syntax/compile regressions, and logic that defeats the feature's own guarantees
(e.g. a gate that can never fire). Output P0 (blocker)/P1 (high)/P2 (medium)/P3 (low), and an
explicit verdict — "ship as-is" if nothing's worth acting on, otherwise the smallest disposition
per issue (fix now/defer/wontfix, one-line reason). List pre-existing/environmental failures
separately so they aren't mistaken for regressions.

If handing this to a fresh Copilot session on Opus 4.8, paste:

```text
Review the changes against .workflow/plan.md as a strict senior engineer, including any "Deviations" it logged during execution. Verify empirically — compile, run tests, trace producer↔consumer, run live queries. Flag: missing error handling, resource leaks, security flaws, syntax/compile regressions, and logic that defeats the feature's own guarantees (e.g. a gate that can never fire). Output P0 (blocker) / P1 (high) / P2 (medium) / P3 (low), and an explicit verdict — "ship as-is" if there's nothing worth acting on, otherwise the smallest disposition per issue (fix now / defer / wontfix, with a one-line reason). List pre-existing/environmental failures separately so they aren't mistaken for regressions.
```

**Optional — quiz before merging:** a diff only gives a light read of what happened, since
behavior depends on existing code paths too. Before pushing anything not 100% understood, ask
one question at a time covering intent, what changed, and any non-obvious existing behavior it now
depends on.

## After Phase 4 — three outcomes

**Clean / "ship as-is":** no P0/P1/P2/P3, or nothing worth acting on. Skip straight to *Final
check & wrap-up* — don't manufacture a patch plan for a clean review.

**P0/P1 present:**
1. **Patch plan** (Copilot Opus 4.8, high): group P0/P1/P2/P3 into a file-by-file patch plan, core
   interfaces first, each with a local check. Save to `.workflow/patch_plan.md`.
2. **Fix P0s** (Codex or Copilot Sonnet 5, high, review each diff): fix only the P0s, one at a
   time, check + commit after each. Don't touch P1/P2 yet.
3. **Fix P1/P2/P3s** (Codex or Copilot Sonnet 5, medium, auto): fix the rest, verify no
   regressions.
4. Re-run Phase 4 review on the fixes before wrap-up.

**Only P2/P3 (no P0/P1):** worth a lighter patch plan — group into a file-by-file patch plan, each
with a local check AND a recommended disposition (fix now/defer/wontfix, one-line reason). Default
to the smallest safe scope. Read the dispositions and decide per item before fixing anything —
don't auto-fix everything listed, especially anything marked "defer." For items to fix now: fix
one at a time, check + commit after each; leave "defer"/"wontfix" alone (confirm the reasoning
still holds, don't implement it). Re-run Phase 4 review on the fixes before wrap-up.

## Final check & wrap-up

Commit, push, curate, and clean up — in one go, in either CLI.

1. Run final checks: build, type-check, full test suite (call out known-environmental failures,
   don't treat them as regressions).
2. Grep for leftover shortcuts: `TODO: Implement`, `NotImplementedError`, `...`, `placeholder`,
   `real implementation`, and any old contract version literal — nothing should still pin it.
3. Commit any remaining changes.
4. Invoke `memory.remember` to route every tagged line in `.workflow/learnings.md` to its
   destination (`MEMORY.md`, `AGENTS.md`, `README.md`, an existing or new skill, `DESIGN.md`),
   commit those changes, and push.
5. Once `memory.remember` confirms every line is routed, delete `.workflow/brainstorm.md`,
   `.workflow/spec.md`, `.workflow/plan.md`, `.workflow/patch_plan.md` (if present), and
   `.workflow/learnings.md`.

**Why this order:** commits are local — nothing leaves the machine until push. Code → learnings
routed and committed → **push** → clear scratch. Delete all the run files, not just the log —
their durable value already lives in the commits and wherever `memory.remember` routed it, and a
stale `plan.md` left behind would poison the next run's re-ground (which trusts the files as
truth). Never delete `learnings.md` before `memory.remember` has actually routed every line — it
enforces this itself, but don't race ahead of it. Open a PR only if not committing straight to
`main`.

## Keeping this skill alive

When this workflow's shape genuinely changes (a new phase, a different model/tool split, a
changed default), edit this file directly — it is the one source of truth; there is no separate
markdown doc to keep in sync.
