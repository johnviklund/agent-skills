# Effort and model choice

| Level | Execution | Independent review |
|---|---|---|
| **Low** | An economical model with light reasoning for clear work. | Local checks; another model only if a concrete risk warrants recommending escalation. |
| **Medium — default** | An everyday capable model with moderate reasoning. | A second model for substantial logic or contract changes; prefer another provider. Simple edits do not need this. |
| **High** | A stronger model with deeper reasoning where the task benefits. | One focused second-model review of the consequential decision or finished change; prefer another provider. |

Use the available model and supported effort that fit the work and the user's allowance.
Recommend a concrete choice when known; otherwise name the capability needed and disclose that
availability is unverified. Respect explicit model choices. Avoid broad model research or a
settings interview for every task. Low/Medium/High express workflow effort, not provider-specific
setting names. A higher level buys reasoning and verification, not extra phases or longer prose.

Keep the main executor responsible for implementation and fixes. Give the reviewer the objective,
constraints, code/diff, and check results so it can form its own judgment. Review where an error
would be expensive to carry forward; do not have both models repeat all the work. Resolve findings
through code, tests, and evidence rather than a model vote. Re-review only when changes warrant it.

Use authorized, available review tools or hand off a concise review request. Do not silently switch
the running model, claim independence that did not happen, or imply a level grants external spend
or subagent authority. If another provider is unavailable, use an available second model or report
the review limitation and continue authorized work; honor any explicit independent-review release
gate. Recommend escalation from Low for a concrete unresolved risk rather than silently increasing
its budget. Historical model mappings remain in the `workflow-v1.0.0` release.
