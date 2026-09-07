# Workflow v2 — choose the process the task needs

`workflow assess <task>` recommends a practical approach after a focused look at the relevant
context. `workflow run <task>` makes the same assessment and continues through authorized work.

Examples:

```text
workflow assess Add a saved filter to the existing dashboard
workflow run Low Fix the empty search label
workflow run High Implement the agreed ROADMAP.md item
workflow execute .workflow/plan.md
```

A clear task goes straight to implementation and verification. A larger task gets a short plan
around usable outcomes. A consequential unknown gets the specific investigation, decision, or
rehearsal needed to resolve it. These are judgments, not separate mandatory tracks.

## One effort choice

Use **Low**, **Medium** (default), or **High** after a work command. Ideas, roadmap items, and
existing plans are all valid inputs. Low favors economical execution; Medium adds a second-model
review for substantial changes; High uses deeper reasoning and focused independent review.
Prefer another provider for that review. All levels keep the plan short and verify correctness.

The skill recommends concrete model settings when available. It does not silently change your
model, equate these levels with provider settings, or guarantee an exact token budget. Model
review is focused on consequential decisions and changes, not repeated at every phase.

## What changed from v1

- No required brainstorm → spec → audit/plan chain before execution.
- No file-by-file approval and commit loop, mandatory resets, or model swaps.
- One concise working plan when needed; later milestones stay coarse until they matter.
- Delivery dependencies surface early. Local tests, deployment, and stakeholder access are
  reported separately.
- Real scope and live-action authorization boundaries remain intact.

The previous skill, including local changes and historical evaluation cases, is preserved in the
[Workflow v1.0.0 release](https://github.com/johnviklund/agent-skills/releases/tag/workflow-v1.0.0).
Extract `.github/skills/workflow/` from that release's source archive to recover it.

## Commands

| Command | Result |
|---|---|
| `assess <task>` | Brief recommendation only; no artifacts or implementation |
| `run <task>` | Assess, then complete the authorized work with appropriate verification |
| `execute [task or plan]` | Start or resume implementation without phase prerequisites |
| `plan [task or artifact]` | Short plan of demonstrable outcomes |
| `brainstorm`, `improve`, `spec` | Focused help with an unresolved direction or contract |
| `review`, `wrap` | Inspect changes or verify and report the actual outcome |
| `status`, `next` | Read-only progress or next-action advice; bare `workflow` means `next` |
| `todo`, `learn`, `log` | Capture intake, useful learning, or a concise worklog entry |
| `bootstrap`, `realign` | Establish needed guidance or compare direction with shipped evidence |

Existing v1 plans remain usable. Resume from verified work and current decisions; do not recreate
missing phases. Assessment is optional, and requesting a plan does not authorize implementation.

## Visible progress

Longer tasks use a short checkbox plan with **Now / Next / Blocked** and an updated timestamp at
the top. The active outcome is marked IN PROGRESS; verified outcomes are checked off with evidence.
The agent updates these markers as work changes and ties progress messages to the same items.
Long active outcomes can have a few observable substeps without expanding the entire plan.

## What to do next

Every workflow response ends with a compact next-action recommendation: what to do, whether to
continue with the current model/session, and the exact command or decision to send. The agent
chooses the next action from the actual state, including outstanding review or learning capture.
A completed checkpoint stays distinct from the delivered outcome. Fully completed work ends
with Done; the recommendation does not force extra phases or interrupt authorized execution.

## Files and installation

`SKILL.md` is the entrypoint. References hold optional command-specific guidance. `ROUTING.md`
defines the three effort levels and how to use a second model.
Historical `evals/` cases remain available for review evaluation.

Install the workflow folder through your existing skill installer or your agent's supported skill
location. This repository's shared symlink setup is described in the root README. Verify discovery
in the target agent; installation paths and switch commands are not assumed universal here.

## Validation

Use the skill-creator validator for frontmatter and unfinished scaffold checks. Exercise realistic
tasks as well: a small direct edit, a task needing a decision, an existing partially finished plan,
and a locally buildable task whose deployment is not authorized. Check observable behavior and
side effects, not exact generated wording or the number of headings.
