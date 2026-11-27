# Renovate 45 configuration and schema cleanup

## Goal

Remove obsolete configuration syntax and misplaced global options without
changing dependency-management policy or the docs lockfile mechanism.
This is a cross-repository project: this spec is its single requirements source.
No implementation or plan is included in this PR.

## Owner decisions

- Keep `requireConfig: "optional"`; repositories without repository configuration
  must not acquire a new requirement to onboard or provide configuration.
- Keep `onboarding: false`.
- Move these options to global configuration in the shared workflow before
  removing them from the shared preset.
- Keep separate spec, plan and implementation approval gates.
- Inventory all owner repositories using Renovate, not only the six currently
  known consumers; assess inherited policies and local migration needs.

Decision source: owner messages in the
[spec discussion](https://discord.com/channels/403461431305043989/1575859314638323844).

## Current state

- `mmalyska/renovate-config/default.json` contains global `onboarding` and
  `requireConfig` options, the deprecated `config:base` preset and
  `dependencyDashboardTitle`.
- Both shared regex-manager presets use `fileMatch`.
- `mmalyska/github-workflows/.github/workflows/run-renovate.yaml` does not
  explicitly set global onboarding or require-config values.
- The reusable workflow runs Renovate through github-action v47; the image's
  major tag is mutable. Record the actual engine version for every comparison.
- Earlier official Renovate 45.1.1 runs succeeded for home-ops and containers.
  Extraction, grouping and exclusions were compared with Renovate 44; the docs
  lock was extracted, but its regeneration was skipped by the schedule.
  That is not evidence of a successful lock regeneration on Renovate 45.
- The separate generic-manager digest fix is already on the default branch;
  this project must preserve its bounded `generic-regex` rule.

## Scope

Production edits are limited to:

- `mmalyska/github-workflows`: global options in `run-renovate.yaml` and
  documentation explaining the shared workflow's policy.
- `mmalyska/renovate-config`: shared JSON/JSON5 presets and their documentation.

Inspect all shared presets for migration diagnostics, including `config:base`,
`fileMatch`, `dependencyDashboardTitle` and any `matchPackagePatterns`
occurrences. Use the Renovate 45 migrator as evidence, not as permission to
accept semantic changes automatically. Preserve regex meaning explicitly when
moving patterns to modern matcher syntax.

The read-only assessment covers all owner repositories using Renovate. The six
known workflow consumers (home-ops, containers, dotfiles, homelab-portal,
argocd-secret-replacer and github-workflows) are a starting point, not a complete
inventory. Enumerate accessible owner repositories and inspect their default
branches for Renovate configuration and execution paths. Record archived or
disabled cases separately and identify inaccessible or uncertain cases rather
than claiming complete coverage from search results alone.

For every discovered consumer, record:

- Execution path: shared workflow and ref, independent workflow, hosted
  integration or another runner, with evidence or an explicit unknown status.
- Repository configuration and resolved preset chain, including whether it
  inherits `mmalyska/renovate-config`, pinned refs and local overrides.
- Effective global settings and their source/precedence; extending a repository
  preset alone is not evidence that global options reach the engine.
- Local schema diagnostics, proposed migrations and any behavior differences
  from shared policy, with source SHA and validation version.
- Disposition: no change needed, local migration candidate, policy decision
  needed, inactive, or blocked by missing evidence.

Validate resolved configurations read-only where access permits. Report local
migration candidates and missing shared-policy links in the assessment; do not
silently attach policies or normalize intentional overrides. The plan must list
affected repositories and proposed changes separately from policy decisions.
This inventory authorization does not expand the production edit scope beyond
the two repositories above. Consumer edits require an explicit owner decision
before implementation.

## Requirements

1. Set global `RENOVATE_ONBOARDING=false` and
   `RENOVATE_REQUIRE_CONFIG=optional` in the Renovate action environment using
   string values. Verify they reach the container's effective configuration.
2. Remove the two options from the shared preset only after the workflow
   change is merged and its effective global settings are verified.
3. Replace obsolete shared syntax with supported Renovate 45 syntax. Verify
   migrated preset references resolve and retain the previous effective policy.
4. Preserve dependency identities, manager selection, file matching, extraction
   and replacement semantics, group membership, schedules, exclusions, labels,
   commit conventions, automerge policy and digest behavior.
5. Preserve no new digest pinning for the two generic managers, and retain
   existing devcontainer and native Docker digest handling.
6. Do not change workflow callers, action or engine versions, caches,
   credentials, permissions, schedules, package pins or docs lock generation.
7. Record source SHAs, preset-resolution provenance, actual tool versions,
   commands and per-check PASS, FAIL or NOT RUN results. Missing evidence is
   not a passing gate.
8. Produce the repository inventory and per-consumer assessment before finalizing
   the migration plan. Account for independent execution paths before removing
   shared-preset globals; verification of the reusable workflow alone does not
   establish coverage for runners that do not use it. Unresolved runner coverage
   blocks preset removal until resolved or explicitly accepted by the owner.

## Acceptance criteria

- The inventory accounts for every accessible owner repository, records the
  discovery method and limitations, and gives every Renovate consumer the
  assessment and disposition specified above. Unknown integration or global
  policy coverage remains explicitly unverified, not assumed compliant.
- The plan separates schema-only consumer migration candidates from policy
  changes and identifies owner decisions needed to authorize either.

- Shared presets validate on the selected exact Renovate 45 version without
  obsolete-syntax or misplaced-global-option diagnostics. Validate resolved
  consumer configurations and distinguish pre-existing local diagnostics.
- Same-version, same-source before/after comparisons establish equivalent
  dependency tuples (manager, file, package, value and digest), not counts alone.
- Matching tests include positive and negative paths for the migrated patterns;
  actual replacement tests cover both generic managers and devcontainer images.
- Rule comparisons preserve groups, schedules, exclusions and digest policy.
- home-ops docs extraction retains pip-compile handling of direct and indirect
  dependencies without duplication through pip_requirements. Compare against
  the pinned source baseline rather than hard-coding a drifting package count.
- Global-option precedence and effective values are exercised with and without
  a repository configuration, using disposable fixtures and no remote writes.
- Workflow lint and documentation checks pass for each applicable PR.
- After the owner's ordered merges, bounded read-only observation of official
  runs confirms effective globals and absence of the targeted diagnostics.
  If no eligible run exists, report observation pending; do not poll indefinitely.

## Rollout and safety

The plan must make verification of the merged workflow change a prerequisite
for shared-preset removal. It must define safe rollback ordering: restore the
preset compatibility state before considering removal of explicit workflow
settings. No direct pushes to default branches, runtime mutations or automatic
merges are authorized. The owner merges every project PR.

Pre-merge fixture evidence and post-merge official-run evidence are separate.
Do not manufacture scheduled updates, disable schedules or claim lockfile
regeneration from extraction alone. Actual docs lock regeneration remains a
separate outstanding verification, not a prerequisite silently waived here.

## Open questions

No additional policy decision is made by expanding the assessment. Missing
policy links, independent-runner coverage gaps, required consumer edits and
unsupported equivalence checks return to the owner before proceeding.
