# Workflow v2 — choose the process the task needs

`workflow assess <task>` recommends a practical approach after a focused look at the relevant
context. `workflow run <task>` makes the same assessment and continues through authorized work.

Examples:

```text
workflow assess Add a saved filter to the existing dashboard
workflow run Fix the empty search state and verify it in the browser
workflow execute .workflow/plan.md
```

A clear task goes straight to implementation and verification. A larger task gets a short plan
around usable outcomes. A consequential unknown gets the specific investigation, decision, or
rehearsal needed to resolve it. These are judgments, not separate mandatory tracks.

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

## Files and installation

`SKILL.md` is the entrypoint. References hold optional command-specific guidance. `ROUTING.md`
contains model-selection principles and is read only when a recommendation or handoff is useful.
Historical `evals/` cases remain available for review evaluation.

Install the workflow folder through your existing skill installer or your agent's supported skill
location. This repository's shared symlink setup is described in the root README. Verify discovery
in the target agent; installation paths and switch commands are not assumed universal here.

## Validation

Use the skill-creator validator for frontmatter and unfinished scaffold checks. Exercise realistic
tasks as well: a small direct edit, a task needing a decision, an existing partially finished plan,
and a locally buildable task whose deployment is not authorized. Check observable behavior and
side effects, not exact generated wording or the number of headings.
