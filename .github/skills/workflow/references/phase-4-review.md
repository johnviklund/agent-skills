# Review — `workflow review`

Review the actual diff against the user's intended behavior and relevant contracts. A plan or
reviewer model handoff is not required. Identify the reviewed revision and any uncommitted diff;
never claim a verdict covers later changes that were not inspected.

Trace likely failure paths, affected consumers, and the important user flow. Confirm findings
against code or meaningful checks. Passing structure tests alone do not establish runtime
behavior. When a defect has repeated call sites, inspect each site's consequences. Treat operator
instructions and durable claims as consumers when contract behavior changes.

Report actionable findings with location, trigger, consequence, severity (P0–P3), and the
smallest useful fix. Separate pre-existing and environmental issues. State when nothing merits
change; do not invent findings, broad hardening, or a patch plan to fill a template.

Standalone review is read-only except for a requested review artifact. Persist `review.md` when
needed for a handoff or consequential release, with coverage, findings, and verdict. During an
authorized implementation run, fix in-scope findings and recheck the affected behavior directly.
A separate patch plan is useful only if the fixes themselves need coordination. Do not loop on
unchanged findings or repeat a whole review merely because supporting prose changed.

Apply the selected level's [model routing](../ROUTING.md) for independent review. Be honest
about coverage and independence; never pretend a model switch or independent review occurred.
A reviewer checks the stated objective against evidence; agreement between models alone does
not prove correctness.
