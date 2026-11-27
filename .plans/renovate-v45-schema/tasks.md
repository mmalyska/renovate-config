# Execution tasks

All tasks are proposed, not dispatched. The owner excluded archived repositories
as unused (message 1575870273436000428), then excluded forks and private
repositories (message 1575872017083924632). Do not audit or modify any excluded
repository or use it as
coverage blockers. Requirements otherwise remain in the merged spec.md.

## H1 - Approve execution (human, unconditional)

Trigger: plan PR available. Owner reviews and merges it, then explicitly
approves
execution in the Discord thread. Evidence: merged PR SHA and owner message.
Blocks all worker tasks. If already verified when execution begins, record and
complete the gate; never ask for the same approval again.

## T1 - Establish reproducible baseline and active-runner coverage

Depends on H1. Evidence-only; no production changes, commits, pushes or PRs.
Scope: read-only public, non-fork, non-archived owner repositories and presets
in
mmalyska/renovate-config and mmalyska/github-workflows. Start from current main
and the inventory snapshot; record actual source SHAs for every read. Exclude
archived repositories, including talos-images, entirely from acceptance gates.

Commands: paginate `gh api users/mmalyska/repos?per_page=100&type=owner`,
inspect
public non-fork non-archived default-branch trees and instruction files, fetch
configuration,
workflow and preset sources using `gh api`. Read recent official action logs
with
`gh run view <id> --repo mmalyska/<repo> --log` without dispatching workflows.
Use `npm exec --yes --package=renovate@45.1.1 -- renovate-config-validator
<file>`
for all shared presets and consumer files, then resolve full active
configurations
with the real engine. Record exact Node/Renovate versions and preset provenance.

Checks: identify all six known active shared workflow callers, their refs,
local overrides and execution-level global sources. Investigate external runner
uncertainty relevant to consumption of the shared preset. Excluded fork
configurations require no runner investigation or policy linking.
Compare resolved policy and baseline dependency tuples including digest fields;
retain baseline fixtures for T2/T4. Local> resolution must be real or explicitly
transformed with documented provenance; unsupported equivalence is not PASS.

Done: sanitized rerunnable commands and per-check PASS/FAIL/NOT RUN evidence in
card comments; inventory deltas, local schema candidates and missing access
identified separately. Effective globals are not inferred from preset
inheritance.
If relevant coverage or baseline cannot be established, stop and invoke H2; no
implementation until resolved. Never print credentials or resolved secret data.

## H2 - Resolve evidence or additional-scope conflict (human, conditional)

Trigger: T1/T2/T3/T4 finds unsupported proof, relevant unknown runner coverage,
policy drift, or need for consumer/external edits. Evidence supplied by blocked
worker must identify precise gap and affected gate. Owner chooses resolution or
explicitly accepts qualified alternative evidence in the thread. Additional
edits
require named repository/files and authorization; approval of this plan is not
it.
Blocks only work dependent on the affected gate. Do not create an inactive
contingency as an unconditional parent or silently waive an exercise.

## T2 - Add explicit shared workflow globals

Depends on accepted T1 and any activated H2 resolution. Repository:
mmalyska/github-workflows, base current main, branch ci/renovate-v45-globals.
Files: .github/workflows/run-renovate.yaml, README.md and narrowly scoped tests/
fixtures if needed. Preserve caller interface, permissions, credentials, cache,
schedule, action SHA and engine selection. No consumer or preset edits.

Set action environment strings RENOVATE_ONBOARDING="false" and
RENOVATE_REQUIRE_CONFIG="optional". Document their global scope, precedence and
the reason they cannot live only in repository presets. Read default-branch
instructions and repository tool config before editing.

Commands/checks: run repository workflow lint (actionlint on the changed
workflow),
Markdown lint on changed docs and `git diff --check`. Using real Renovate 45.1.1
and disposable files, exercise each global with and without repository config,
including conflicting lower-precedence settings. Assert parsed false is boolean
false and requireConfig remains optional; prove no onboarding/config requirement
is introduced. Record command, version, configuration sources and outcomes, not
merely YAML key presence. Container forwarding remains a separate T3 gate.

Done: all required local gates PASS; worker commits and pushes the branch and
posts full SHA, scoped diff and sanitized evidence in card comments; no PR
opened
by worker. Conductor audits the evidence and opens the workflow implementation
PR.
If a required tool cannot run, stop; do not replace real-tool proof with a mock.

## H3 - Merge workflow PR (human, unconditional)

Trigger: T2 conductor review and local gates accepted, PR opened with actual CI
status. Owner reviews and merges. Evidence: merged PR and merge SHA verified by
`gh pr view`. Blocks T3 and all preset removal. No auto-merge or workflow
dispatch.

## T3 - Verify merged workflow in the official container

Depends on H3. Evidence-only. Read-only mmalyska/github-workflows plus active
callers; no production changes, workflow dispatch, dependency PR creation or
push.
Inspect at most one latest qualifying completed official run per active shared
caller using `gh run list` and `gh run view --log`; record actual workflow
revision,
action SHA and Renovate/image version. A qualifying run uses the merged T2 code.

Checks: sanitized engine-level evidence confirms onboarding=false and
requireConfig=optional actually reach the action container; distinguish
input/env
configuration from observed effective values. Check all active execution paths
recorded by T1. Repository-config/no-config behavioral proof remains T2's
fixture
exercise; do not run a live no-config onboarding experiment.

Done: exact run links, source versions and effective-value evidence for required
paths; accepted T3 is mandatory before T4 removes shared globals. If no eligible
run or observable values exist, report pending and stop at a read-only handoff.
No indefinite polling. Owner can trigger a later bounded retry or decide H2;
absence of a run is not a PASS and does not authorize a workflow dispatch.

## T4 - Migrate shared syntax without policy drift

Depends on accepted T1 and T3 and any active H2 resolution. Repository:
mmalyska/renovate-config; base current main, branch chore/renovate-v45-schema.
Files: shared JSON/JSON5 presets, README.md, narrowly scoped tests/ fixtures and
real-engine regression scripts. No edits to consumers or docs dependency locks.

Inspect every shared preset. Run Renovate 45.1.1 migrator/validator and review
its proposed changes, including config:base, fileMatch, dependencyDashboardTitle
and any matchPackagePatterns. Remove onboarding/requireConfig from default.json
only after verifying T3 acceptance. Keep every other policy unchanged. Modern
regex delimiters must preserve meaning; built-in preset renames need resolved
policy equivalence, not a textual rename assumption.

Required commands/checks, recorded against baseline and final full SHAs:

- Run `npm exec --yes --package=renovate@45.1.1 -- renovate-config-validator
  <file>` on all shared presets and validate fully resolved active consumers.
  No obsolete shared syntax/global warnings; separate existing local warnings.
- Compare same-engine, same-source extraction tuples (manager, file, package,
  current value, digest) before/after, plus resolved rules, group membership,
  schedules, labels, commit conventions, automerge and excluded paths.
- Use real engine matching and replacement APIs on disposable fixtures; inspect
  installed entry points rather than assuming internal signatures. Exercise
  positive/negative paths, both generic managers and devcontainer replacements,
  version-only updates preserving digests, explicit digest changes and unpinned
  inputs without newDigest. Native Docker/devcontainer pinning stays unchanged;
  generic suppression remains restricted to its existing depType/rule boundary.
- Fully resolved home-ops extraction preserves pip-compile direct/indirect
  ownership and no pip_requirements duplication. Compare source-derived tuples,
  not a hard-coded count. Do not claim regeneration from extraction.
- Run changed-file Markdown lint, repository checks and `git diff --check`.
  Publish reproducible fixture provenance and scripts or commands in comments;
  a file path in a worker workspace is not a handoff.

Done: required local exercises PASS with semantic output audited, branch
committed
and pushed, full SHA and evidence in card comments. Add commits, never amend
prior
evidence commits. Worker opens no PR. Conductor fetches and reviews
diff/evidence,
reruns cheap gates and opens the shared-preset implementation PR. A missing
local
acceptance exercise must not be deferred into post-merge observation.

## H4 - Merge shared preset PR (human, unconditional)

Trigger: T4 accepted, conductor opens PR, local gates accepted and CI reported
accurately. Owner reviews and merges. Evidence: merged PR SHA verified through
GitHub. Blocks T5. Do not approve or merge this PR as the orchestrator.

## T5 - Observe migration and hand off outstanding lock proof

Depends on H4. Evidence-only, read-only active shared consumers and official run
logs. Inspect at most the latest qualifying completed run for each active shared
execution path, recording preset source and actual engine versions. No dispatch,
remote writes, changed schedules, package bumps or manufactured update events.

Checks: effective globals unchanged, targeted shared diagnostics gone, no new
extraction/group/exclusion/digest regression. Compare only like sources/versions
or explain drift; moving :45 tags are not same-version proof. Keep pre-existing
consumer diagnostics separate. If a naturally eligible docs update exists,
inspect
actual lock regeneration command, tool versions, changed hashes and resulting
checks. Extraction or a schedule-skipped branch is not regeneration evidence.

Done: bounded report with run URLs and PASS/FAIL/NOT RUN per item. If a
qualifying
run or docs event is absent, leave an explicit pending handoff for an
owner-triggered
later read-only check; never poll indefinitely. Do not mark migration
observation
accepted when required globals/diagnostics remain unverified. Actual docs lock
regeneration stays separately outstanding until observed, without blocking
accepted
schema equivalence or silently being declared verified.

## H5 - Recovery approval (human, conditional)

Trigger: observed policy drift or failed rollout. Conductor proposes scoped
rollback
PRs; owner reviews and merges them. First restore shared preset compatibility
and
verify it, then consider removing explicit workflow globals. Preserve the
earlier
bounded generic digest fix. Blocks further rollout until restoration is
verified.
No direct branch reset, deployment or cluster mutation is authorized.
