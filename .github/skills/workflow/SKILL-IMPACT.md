# SKILL-IMPACT — changes to these skills, and what the runs after them showed

Mode: autonomous          # autonomous = memory.remember applies skill edits itself, in their own
                          # commit; approve = it writes SKILL.md.proposed and you accept with mv

One line per change, newest first. A change is *trialed* on the runs that follow (their worklog
entries carry `Skills: <skill>@<sha>`); `checkup seats` compares `Run:` numbers before/after.
Reverted and rejected changes are logged too, so they are not re-proposed. Outcomes: `proposed` · `trialing` · `kept` · `reverted` · `rejected`.

| Date | Skill | Change | From | Trial until | Outcome |
|---|---|---|---|---|---|
| 2026-09-12 | workflow · memory.remember · memory.compact | v2.01: static-review fixes — code identity by receipt rule (review receipts no longer stale the gate), resumable `wrap.md`, `workflow execute` runs patch plans, open-finding gate (`Resolved:` stamps, human-approved P0/P1 deferral), per-file freshness check, brainstorm coverage in plan and review, idempotent routing (`[routed → …]`, unique evidence), guarded compact acceptance, tracked `.workflow/` enforced at bootstrap and brainstorm | external static review | 3 runs | trialing |
| 2026-09-12 | workflow | v2: one run per `.workflow/<slug>/`, runs kept as archive, `park`, `memory/` pages, trial-gated skill changes | design session | 3 runs | trialing |
| 2026-09-12 | workflow | reporting rule, plan budget (≤100 lines / ≤12 steps), scope-locked executor, optional spec, vendor-only routing, trial column | design session | 3 runs | trialing |
| 2026-09-11 | evals / checkup | exams reduced to a ≤8-case reviewer recall check; seats judged on trial runs | design session | — | applied |
