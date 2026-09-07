# Golden case — code-review — a patch fixed one of two structurally identical call sites

## Case shape
`code-review` (seat: `strict-reviewer`)

## Input (diff / text under review)

A follow-up patch responding to a review finding: when the workspace-settings config request
fails, the frontend omits `workspaceId` from subsequent requests, and the backend silently
defaults to `default_workspace` — so the user is shown another workspace's engagement state
without any indication that the workspace they're viewing was substituted.

The finding, as recorded, named `FrictionPulsePage` as where the behavior was observed. The patch
under review widens the banner copy on `FrictionPulsePage` to disclose the fallback. The construct
being patched is: `...(resolvedWorkspaceId ? { workspaceId: resolvedWorkspaceId } : {})` — spread
into the request params only when a workspace id resolved, silently omitting it otherwise.

`FrictionTopicPage` contains the byte-identical construct
(`web-ui/src/pages/FrictionTopicPage.tsx:163-174`) and was left untouched by this patch.

## The bug a passing review must catch

The fix is scoped to the file the original finding happened to name, not to every place the same
defective construct appears. `FrictionTopicPage` has the identical silent-fallback pattern, and
the consequence there is *worse* than on Pulse: there is no page-level banner or notice of any
kind — the only signal is a hover-only `title` tooltip, easy to miss — and the page actively
contradicts itself. The hero badges read "Feedback Received" / attention-requested state from the
fallback workspace, while the history list below reads "No governance feedback received," because
the separate community-feedback-history request is skipped entirely on the same null
`workspaceId` that the detail request silently defaulted away. A user sees a state ("feedback
received") with no evidence for it directly below.

Nothing in the existing verification catches this: the full backend suite passes (backend
behavior is unchanged and correct — it's the frontend disclosure that's incomplete), the whole
web build chain passes, and the patch plan's own step check — "grep the new banner copy string →
exactly one hit" — passes precisely *because* the fix is narrow: it greps for the new copy it just
added, which by construction only exists in the one file just edited.

## Approved finding (what a passing answer must contain)

- Identifies that the fix widened copy on the file the finding *named*, but the defect is a
  *construct* (`resolvedWorkspaceId ? {...} : {}` silently omitting the id on failure), not a
  file — and that construct appears in a second, untouched file.
- Cites the second site precisely: `FrictionTopicPage.tsx:163-174`, byte-identical to the fixed
  construct.
- Independently traces the second site's consequences rather than assuming they match the first:
  notes that `FrictionTopicPage` has *no* banner mechanism to widen (unlike Pulse, which already
  had one), and that the resulting contradiction (badges say "received," history list says "none
  received") is a distinct, worse defect than the first site's silent substitution — the history
  request 422s and is skipped outright when `workspaceId` is omitted.
- States the generalizable rule: a fix scoped to the call site a finding named is not complete
  until every structurally identical call site has been enumerated (e.g., grep the construct
  repo-wide) and each site's consequences checked on its own terms, since sites with different
  surrounding UI can fail in different, non-transferable ways.
- Notes that the patch plan's own "grep the new copy → 1 hit" check cannot detect this gap by
  construction, because it only searches for text the fix itself introduced in the one file it
  touched.

## Grading notes / traps

- **Fails:** approving because the specific finding's originally-named file (`FrictionPulsePage`)
  now has correct behavior and its own verification step passes.
- **Fails:** assuming the two pages are equivalent enough that "the same fix pattern would work" is
  sufficient reasoning, without checking that `FrictionTopicPage` actually has a banner slot to
  extend (it doesn't) or that its consequences are structurally different (the badge/history
  contradiction).
- **Partial credit:** noticing the construct recurs elsewhere without also tracing what specifically
  goes wrong on the second page.
- **Full credit** requires citing the exact recurrence location, explaining why its failure mode
  differs from (and is worse than) the fixed site, and stating the transferable enumerate-every-
  site rule as the reason the patch plan's own check was insufficient to catch this.

## Provenance

- Date: 2026-08-29
- Repo: CX Intelligence (`cx-intelligence`)
- Source artifacts: `.workflow/learnings.md` (durable→eval entry, cycle 2), `.workflow/review.md`
  → Cycle 2, P2, diff `6ee3499..b6deb41`,
  `web-ui/src/pages/FrictionPulsePage.tsx` (fixed) vs.
  `web-ui/src/pages/FrictionTopicPage.tsx:163-174` (identical, unfixed at the time of this finding)
- Reproducer: write a `request_attention` row to `default_workspace` only, then `GET
  /topics/{key}` with `workspaceId` omitted → `attentionRequested: true`, while `GET
  /topics/{key}?workspaceId=<configured>` → `attentionRequested: false`; `GET
  /topics/{key}/community-feedback` without `workspaceId` → `422`, which is why the history
  renders empty beside the populated badges.
- Produced by: writer model implementing CG2 (workspace engagement state); found by:
  strict-reviewer seat, Phase 4 review, cycle 2
- Approved by: human, via the `workflow review` → patch-plan cycle, fixed by adding the matching
  banner to `FrictionTopicPage` in cycle 2's patch; verified closed at cycle 3 by enumerating the
  construct repo-wide and confirming exactly two call sites, both bannered.
