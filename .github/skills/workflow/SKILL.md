---
name: workflow
description: >
  Use only when explicitly invoked as workflow COMMAND, /workflow COMMAND, or by
  selecting the workflow skill. Assess a task and recommend the smallest useful
  process, or execute it with proportionate planning and verification. Commands:
  assess, run, brainstorm, improve, spec, plan, execute, review, wrap, status, next,
  learn, log, todo, bootstrap, realign. Bare workflow means next. Casual mentions
  of planning, execution, or review do not invoke this skill.
---

# Workflow v2

Get the user's intended outcome working and verified. Choose preparation to resolve real
uncertainty; do not make an already clear task pass through a fixed chain of phases.

## Entry commands

- `workflow assess <task or artifact path>` inspects enough context to recommend an approach.
  It is advisory: no implementation, plan file, commits, or external mutations. Read
  [assessment](references/assess.md).
- `workflow run <task or artifact path>` makes the same short assessment, then continues
  through the authorized work, verification, and outcome report. It does not stop for the user
  to invoke the recommended command. Read [assessment](references/assess.md), then only the
  reference needed for the work that follows.
- `workflow execute [task or plan path]` starts or resumes implementation directly. A separate
  brainstorm, spec, or plan file is not a prerequisite. Read [execution](references/phase-3-execute.md).
- `workflow status` reports the intended outcome, verified progress, actual blockers, and next
  useful action. `workflow next` (also bare `workflow`) recommends that action and a command;
  both are advisory. Use current conversation and relevant artifacts, not a phase-state table.

An explicitly requested command bounds the work: `plan` produces a plan, `review` reviews,
`assess` recommends. A later instruction to continue or implement authorizes continuing within
that scope. Do not reinterpret an advisory request as permission to build.

## Effort: Low, Medium, High

Work commands accept an optional level after the command, for example `workflow run Low <idea>`
or `workflow assess High ROADMAP.md — <item>`. Use **Medium** when omitted; retain an explicit
level through the task until the user changes it. Read [routing](ROUTING.md) to apply the level.
The level controls model effort and useful independent review, not plan length or scope. It is
not an exact token cap or a literal provider reasoning setting. Keep checks necessary for
correctness at every level. Assessment recommends; it never switches models or starts a review.

## Choose the smallest useful process

| Evidence about the task | Approach |
|---|---|
| Clear change with a known local verification | Inspect, implement, verify. No planning artifact needed. |
| Clear outcome involving several dependent changes | Short plan of demonstrable outcomes, then execute the first coherent slice. |
| Important unknown about behavior, design, integration, or feasibility | Resolve that particular unknown through a focused inspection, experiment, or question; then proceed. |
| Migration, expensive operation, or consequential release | Add the specific contract, rehearsal, recovery, or approval work its actual risk requires. |

These approaches can combine. File count alone does not require a spec; a schema edit alone
does not require a new interview. A failing feature usually needs diagnosis, not brainstorming.
Stop preparing when the next coherent change and its verification are clear. Reassess only
when new evidence changes the approach, not before every step.

## Grounding and scope

Read applicable `AGENTS.md`, the user's named inputs, and the relevant source or diff. Consult
memory, product/design documents, roadmap, or TODO entries only where they affect this task.
Check Git state before edits so existing work is preserved. Do not scan every installed skill
or expand the task to adjacent TODO items; load a skill when its guidance is actually needed.

Use settled decisions and authorization already in the conversation. Ask only when a missing
answer materially changes the outcome and cannot be established from available evidence.
Continue independent work while a necessary decision is pending. Resolve routine implementation
choices and failures yourself. Do not require approval of each local diff.

Task authorization does not imply unrelated actions. Honor explicit limits on spend, live data,
promotion, publication, or deployment. Prepare the concrete change, checks, recovery, and any
cost cap before requesting missing authority. Do not ask again for authority already granted.
Surface a delivery dependency such as hosting, credentials, or access early; make progress on
it within scope while implementation continues.

## State and continuity

For work spanning sessions, keep one concise `.workflow/plan.md` (or the user's existing plan):
objective and scope; next demonstrable outcomes and checks; material decisions; current state
and blockers; links to evidence. Detail the next slice, with later work coarse. Update at useful
checkpoints or before handoff. Keep execution history in commits or linked evidence instead of
appending every command and approval to the plan.

A new task must not overwrite an unrelated active plan. Name a separate task file when needed.
For a persisted artifact, record its task, date, source revision if available, and whether it is
still being drafted or ready for its stated use. Status describes the artifact, not delivery.
`drafting` forbids blindly treating the whole document as executable; verified, settled work
can proceed from the user's clear instructions while an unrelated section remains unresolved.

Existing v1 artifacts are inputs, not gates. Resume relevant unfinished work after checking it
against current code and later user decisions. Preserve meaningful decisions and evidence; do
not regenerate missing phases, reset completed work, or equate checked steps with deployment.
Revalidate stale assumptions where they matter rather than rejecting a whole plan on timestamps.

Keep one executor's context through a coherent change. Use the selected level's routing for
capability and independent review; reset or hand off only when it helps that work. No required
phase-by-phase model swaps or next-step cards.

## Model-only attribution

In saved artifacts, logs, worklogs, review/handoff records, commit messages, and PR descriptions,
identify the writer or reviewer only by the actual model used. Never add its provider, vendor,
harness, or coding-agent product. Strip provider branding from model display names: use fields
such as `Writer: GPT-6 Astra` and `Reviewer: Sonnet 5`. If the model is unknown, say unknown;
do not infer it from the tool. Apply the same rule to prose and generated metadata. Review
independence may be recorded generically as independent or self-review, without provider names.
This governs agent attribution; preserve task-relevant technical facts, commands, and evidence.

## Completion

Verify behavior against the user's objective, with checks appropriate to the change. Report
what works, the evidence, and what remains. Distinguish implemented, locally verified, deployed,
and accessible when relevant. If delivery is blocked, state the actual dependency and next
action; do not mark the objective complete because preparation is complete.

Commits should group coherent changes, not individual files. Commit, push, open a PR, or publish
when requested or authorized by the task; a workflow command alone grants none of those beyond
its described scope. Keep historical evidence and unrelated work intact.

## Other commands

These are optional tools, not sequential prerequisites. Read only the applicable reference.

| Command | Purpose and reference |
|---|---|
| `brainstorm`, `improve` | Resolve unclear direction or inspect an improvement opportunity: [direction](references/phase-0-brainstorm.md) |
| `spec` | Settle a contract or design that needs a durable explanation: [spec](references/phase-1-spec.md) |
| `plan` | Produce a short executable plan from the request or existing material: [plan](references/phase-2-plan.md) |
| `review` | Review the actual change and its behavior: [review](references/phase-4-review.md) |
| `wrap` | Verify the outcome, update affected docs, and report remaining delivery: [wrap](references/wrap.md) |
| `learn`, `log` | Capture useful lessons or a concise worklog entry: [learning](references/learning-worklog.md) |
| `todo` | Record the user's idea without starting implementation: [intake](references/todo.md) |
| `bootstrap` | Establish only the project guidance needed now: [bootstrap](references/bootstrap.md) |
| `realign` | Compare product direction with shipped evidence: [realignment](references/realign.md) |

Maintain this skill by replacing rules that cause demonstrated friction. Add detail only when
it changes a decision or protects a concrete invariant; do not accumulate procedures for every
past failure. Historical evaluation cases remain evidence, not additional workflow instructions.
