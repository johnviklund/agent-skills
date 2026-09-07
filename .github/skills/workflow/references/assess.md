# Assess the task — `workflow assess` / entry to `workflow run`

Establish what the user wants to be observably true, what is already settled, and what uncertainty
or dependency actually prevents the next useful change. Reuse context rather than interviewing
again. With no identifiable task, ask for it; do not invent a project from old artifacts.

Inspect the smallest relevant source/diff and applicable instructions. Follow a dependency only
if it could change the recommendation. This is a short routing judgment, not a repository audit,
scoring framework, full skill inventory, or preliminary specification.

Recommend one approach in a brief paragraph or a few bullets, normally under 200 words:
- the intended outcome;
- the recommended approach and the concrete reason;
- the next useful change and how to verify it;
- a material decision or delivery dependency, only if one exists.

For a complex task, identify the first useful end-to-end slice and the risky assumption to test.
Do not generate a file-by-file backlog for later milestones. Keep the full requested objective
visible: choosing a first slice is sequencing, not permission to drop the rest.

`assess` ends with a usable next command, without writing artifacts or starting implementation.
`run` gives the recommendation and proceeds under the hub's authorization rules; it can make a
short plan or resolve an uncertainty inline without requiring another command. An unresolved
product choice blocks only the work that depends on its answer.

Examples of judgment, not rigid categories:
- Correct a known label: edit and inspect the rendered result.
- Add a filter through an existing API and UI: small implementation plan, verify the complete flow.
- Investigate a timeout: reproduce and trace the failing boundary before deciding on a fix.
- Replace a live schema: inspect consumers, rehearse migration/recovery, then reach the authorized
  deployment boundary with a concrete package. Local implementation need not await live authority.
