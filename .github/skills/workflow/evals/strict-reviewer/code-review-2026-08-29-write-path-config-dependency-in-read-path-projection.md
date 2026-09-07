# Golden case — code-review — a write-path config dependency moved into a read-path projection

## Case shape
`code-review` (seat: `strict-reviewer`)

## Input (diff / text under review)

Backend Python for a new "workspace engagement state" feature (bookmark/comment/request-attention/
community-feedback signals) on the CX Intelligence topic detail and Pulse endpoints.

The diff adds a required config field and a resolver for it,
`community_contributor_resolver_from_config`, then calls that resolver from inside
`_topic_engagement_payload` (`foundry/cx_intelligence_backend/repository.py`) — the shared
projection function used by both `/pulse/current` (once per topic in the list) and
`/topics/{key}` (once per request). The pre-existing sibling resolver, `workspace_role_resolver`,
has exactly one call site and it sits off the read path entirely (used only for a write-time
authorization check), establishing that pulling a config-dependent resolver *into* a per-item read
projection is new behavior, not an existing pattern being reused.

The same plan that introduces this change also states, elsewhere, that a forced settings failure
should "disable the bookmark and feedback controls **without blanking Pulse**" — i.e. Pulse must
degrade gracefully, not error out, when workspace config is unavailable.

## The bug a passing review must catch

`_topic_engagement_payload` now transitively depends on workspace config resolving successfully,
for every topic, on every call to `/pulse/current` and `/topics/{key}`. If the config file is
missing, unreadable, or otherwise fails to load, the resolver raises, and because it is invoked
per-item inside the shared projection, the exception propagates up through both endpoints and
produces a full `500`. This directly contradicts the plan's own promise that a settings failure
degrades gracefully rather than blanking Pulse. The full test suite passes, because every test in
the suite supplies a valid config — none exercises what happens when the resolver's underlying
config read fails. The defect is real, but structurally invisible to a suite built entirely on the
happy path.

## Approved finding (what a passing answer must contain)

- Names the concrete propagation path: `community_contributor_resolver_from_config` is called
  inside `_topic_engagement_payload`, shared by `/pulse/current` (per topic) and `/topics/{key}`,
  so a config failure anywhere in that resolver 500s both endpoints, not just one feature.
- Cites the contradiction directly: the plan states a settings failure must degrade the
  bookmark/feedback controls "without blanking Pulse," and this implementation does the opposite
  for every topic on the Pulse list.
- States the generalizable check explicitly: when a diff adds a new required config field and a
  resolver over it, find every read path that now transitively depends on that resolver, and
  verify each one's failure mode is degradation, not an unhandled exception.
- Notes that passing tests are not evidence here, because the suite's fixtures construct valid
  config by definition and no test breaks the config to observe the failure path.
- Severity P1 — a config problem entirely unrelated to a specific topic can blank the whole Pulse
  surface with a `500`, not just disable one topic's community controls.

## Grading notes / traps

- **Fails:** approving because the diff's own new tests pass — those tests all supply valid
  config, so they cannot see this failure mode by construction.
- **Fails:** treating the resolver call as safe because a structurally similar sibling resolver
  (`workspace_role_resolver`) already exists elsewhere in the codebase, without checking whether
  that sibling's call site is on the read path (it isn't — it's write-time only).
- **Partial credit:** flagging "this resolver could throw" in the abstract without connecting it
  to the plan's own explicit degrade-gracefully promise, or without naming which endpoints are
  affected and how often (per-topic, not once-per-request).
- **Full credit** requires (a) tracing the call from the resolver into the shared projection
  function, (b) naming both affected endpoints and the granularity (per topic in the Pulse list),
  and (c) citing the plan's contradicting promise as the reason this is a defect and not a design
  choice.

## Provenance

- Date: 2026-08-29
- Repo: CX Intelligence (`cx-intelligence`)
- Source artifacts: `.workflow/learnings.md` (durable→eval entry, cycle 1), `.workflow/review.md`
  → Cycle 1, P1, diff `fb1ae35..6ee3499`,
  `foundry/cx_intelligence_backend/repository.py`
  (`community_contributor_resolver_from_config`, `_topic_engagement_payload`)
- Reproducer: build the app with a `business_intent_config.json` whose `community_contributor` key
  is removed, then `GET /pulse/current` → `500`.
- Produced by: writer model implementing CG2 (workspace engagement state); found by:
  strict-reviewer seat, Phase 4 review, cycle 1
- Approved by: human, via the `workflow review` → patch-plan cycle, fixed by making the
  contributor-context resolution degrade to withholding the contributor claim rather than raising;
  verified closed at cycles 2 and 3.
