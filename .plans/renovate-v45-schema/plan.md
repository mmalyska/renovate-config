---
status: proposed
size: big
profile: STANDARD
topology: HANDOFF
pr: 4
---

# Renovate 45 migration plan

## Requirements and baseline

[spec.md](spec.md) is the merged requirements source (spec PR #3,
merge b21cce1edd2e3a2ca1a86d99dc9881d21ce0d5ad), unchanged here.
The owner authorized this separate plan PR, not implementation.
The owner subsequently excluded all archived repositories as unused (message
1575870273436000428): no archived repository gates or migration tasks apply.
[Repository assessment](inventory.md) records the 28-repository snapshot,
six active shared consumers, four independent configs,
local migration candidates, source SHAs and unresolved runner coverage.
There is no repository plan checker or existing plan-layout convention on main;
this PR introduces a minimal active-plan index without changing repository rules.

## Design and edit boundaries

1. Capture reproducible same-source baselines and resolve relevant runner gaps.
2. In github-workflows, set action environment string values
   RENOVATE_ONBOARDING="false" and RENOVATE_REQUIRE_CONFIG="optional"; document
   their scope and precedence. Preserve interface, action SHA, permissions,
   credentials, schedules, caches and engine selection.
3. Only after owner merge and effective-container verification, migrate shared
   presets in renovate-config. Use the exact-version migrator as a proposal;
   inspect config:base, fileMatch, dependencyDashboardTitle and any obsolete
   matchers across all presets. Do not assume modern regex/glob equivalence.
4. Keep generic-regex digest suppression bounded to its existing selector;
   preserve devcontainer/native Docker behavior and all consumer policy.
5. Observe official runs read-only after ordered owner merges. No workflow
   dispatch, remote dependency update, direct main push or auto-merge is implied.

Production scope is run-renovate.yaml plus its README documentation in
mmalyska/github-workflows, and shared JSON/JSON5 presets plus README in
mmalyska/renovate-config. Regression fixtures/scripts for those changes may be
added under tests/ in the respective repository. Consumers stay read-only.
Home-ops schema candidates require separate authorization;
independent configs must not be silently made consumers of shared policy.

## Delivery sequence and gates

- H1: owner merges this plan and explicitly authorizes execution in the thread.
- T1: read-only baseline, resolved-config assessment and coverage gate.
- H2: conditional owner decision ONLY if T1 cannot establish relevant coverage
  or equivalent behavior. Inactive unless triggered; not an automatic waiver.
- T2: workflow globals implementation and local fixture/lint gates; conductor
  audits the pushed branch and opens the first implementation PR.
- H3: owner reviews and merges workflow PR; T3 reads a qualifying official run
  to verify actual globals before any removal from shared presets.
- T4: shared preset migration and complete local equivalence/replacement gates;
  conductor audits and opens the second implementation PR.
- H4: owner reviews and merges shared preset PR.
- T5: bounded read-only official-run observation and outstanding docs-lock
  verification handoff; never manufacture an eligible dependency update.

Task acceptance is not implied by a done card. Every required local check must
have actual evidence before PR delivery; absent CI is reported separately.
No worker cards are created during the plan phase. The conductor owns final
review, PR publication and phase transitions; workers never merge project PRs.

## Verification strategy

Use Renovate 45.1.1 for paired local comparisons, record Node and tool versions,
source and preset SHAs, commands and PASS/FAIL/NOT RUN per exercise. If production
:45 moves, record the official engine version separately; a changed engine does
not substitute for same-version comparisons. Validate standalone presets AND
resolved consumers, including local> inheritance with explicit provenance.

Compare normalized dependency tuples (manager, file, package, value, digest),
resolved rule fields and group membership, not totals alone. Exercise positive
and negative path matches, real generic/devcontainer replacement, digest
boundaries, schedules, exclusions and docs pip-compile direct/indirect ownership.
Use disposable configs to test both global values with/without repository config
and conflicting lower-precedence values, without remote writes. Action/container
propagation is a separate post-merge gate, not established by YAML or npm tests.

Actual docs lock regeneration remains outstanding: extraction is not proof of
hash regeneration. T5 reports it pending if no eligible official event exists.
It does not change pins, schedules or the lock mechanism to force an event.

## Rollback and stop conditions

On policy drift, missing relevant runner coverage or unsupported equivalence,
stop before preset removal and return evidence to the owner. No implicit strategy
substitution or additional consumer edit is authorized.

Rollback is owner-merged PRs only: first restore shared preset compatibility
(including misplaced globals if necessary), verify restoration, and only then
consider removing explicit workflow settings. Leaving redundant globals in the
workflow is safer than removing the only verified engine-level source first.
Do not roll back the previously merged generic digest fix as collateral cleanup.

## Completion

Owner-merged ordered implementation PRs, accepted local evidence and bounded
post-merge observation establish migration completion. Pending official runs and
actual docs regeneration remain named open handoffs, not fabricated passes.
No deployment, repository settings change or live cluster action is part of this
plan. Human tasks and their exact blocking relationships are in tasks.md.
