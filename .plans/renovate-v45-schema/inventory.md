# Repository assessment

## Owner scope clarification

The owner excluded archived repositories because they are no longer used
(Discord message 1575870273436000428). They require no migration, validation,
runner coverage, or approval gate in this project. This clarification narrows
the archived-case disposition in spec.md. The owner further excluded forks and
private repositories (message 1575872017083924632); all other requirements
remain
intact.

## Method and limits

Read-only snapshot on 2026-11-27: paginated `users/mmalyska/repos` (28 public
repositories), cross-checked against authenticated `user/repos` (10 visible
owner repositories, all already included). The initial ledger included 28:
9 forks, 0 private repositories and 9 archived repositories (no overlap between
forks and archived entries). Excluding these leaves 10 repositories, versus 19
non-archived entries before the fork/private filter. GitHub fork/private/archive
flags were rechecked on 2026-11-27; private repositories were not in the
original
public snapshot and are excluded from further discovery. Default-branch
recursive trees were
read for all 28; none was truncated or failed. Repository instruction paths were
fetched before configuration content. Sources below are commit SHAs, not tree
object IDs, despite the local discovery script's `sha` field name.

Reviewed Renovate-named configuration candidates and workflow YAML content.
Workflow mentions in lint, CI and metadata jobs were distinguished from actual
Renovate execution. No production files were edited or workflows dispatched.
No claim is made about private repositories unavailable to this account, hosted
app installation settings, external runners, or configuration embedded in
arbitrarily named files. Those are coverage limits, not evidence of absence.
Historical archived cluster manifests in home-ops are not evidence of an active
runner. T1 must resolve relevant external-runner uncertainty before removal of
global options, or return it to the owner explicitly.

Standalone file validation used Node 26.13.1 and Renovate 45.1.1:
`npm exec --yes --package=renovate@45.1.1 -- renovate-config-validator <file>`.
The initial validation of 17 consumer files returned exit 0, including files
subsequently excluded by archive/fork filtering. Only retained consumer results
are relevant to acceptance. This validates
individual files and reports migrations; it does NOT prove recursively resolved
preset equivalence, effective global options, extraction or replacement.
Resolved execution and precedence proof remain T1/T3 gates.

## Shared-policy consumers

Six active consumers call
`mmalyska/github-workflows/.github/workflows/run-renovate.yaml@main` from
`.github/workflows/scheduled-renovate.yaml` and extend the unpinned
`github>mmalyska/renovate-config` default preset. That preset extends built-ins
and shared commit-message, pr-labels, semantic-commits, generic-regex-manager
and devcontainer-regex-manager presets. Resolve all of them at recorded SHAs in
T1. Their configured shared values are onboarding=false and
requireConfig=optional
in the preset; the reusable workflow currently lacks the corresponding explicit
environment settings. Effective engine values are NOT VERIFIED by inheritance.

- argocd-secret-replacer: timezone, author and reviewer overrides; standalone
  validation exit 0 without local migration warning; no local schema candidate.
- containers: custom azerothcore SHA manager, patch release age, weekly
  image/tool
  groups, goss group and conda registry override; standalone validation exit 0
  without local migration warning. Preserve these policies; no local edit
planned.
- dotfiles: custom accepted plugin tag+SHA replacement and manual acceptance
  rule;
  standalone validation exit 0 without local migration warning. Preserve its
  explicit automerge=false; no policy normalization or local edit planned.
- github-workflows: author, username, platform and repository list overrides;
  standalone validation exit 0 without local migration warning. Review global
  context separately because this file is also passed as action configuration.
- home-ops: local> preset chain includes allowedVersions, autoMerge,
  disabledDatasources, groups, managers and customManagers; local ignorePaths,
  reviewers and author. Standalone validation reports migrations in managers,
  customManagers and groups (fileMatch and matchPackagePatterns). Other local
  files return exit 0 without migration warnings. Candidate schema-only
follow-up
  requires separate owner authorization. Existing version limits, digest
exceptions,
  group rules and docs pip-compile/indirect dependency handling stay unchanged.
- homelab-portal: patch/dev-tool release ages, Expo/native module constraints,
  major restrictions and Java version exclusion; standalone validation exit 0
  without local migration warning. Intentional differences remain untouched.

## In-scope repository ledger

Only public, non-fork, non-archived owner repositories are included.
Source links refer to the inspected default-branch commits.

- [argocd-secret-replacer]: shared-policy consumer.
- [claude-plugins]: no identified config/runner.
- [containers]: shared-policy consumer.
- [dotfiles]: shared-policy consumer.
- [github-workflows]: shared-policy consumer.
- [helm-charts]: no identified config/runner.
- [home-ops]: shared-policy consumer.
- [homelab-portal]: shared-policy consumer.
- [renovate-config]: shared preset publisher.
- [vintagestory-mods-downloader]: no identified config/runner.

## Proposed changes versus decisions

Authorized implementation scope remains github-workflows and renovate-config
only: add explicit workflow globals, verify their propagation, then migrate the
shared preset syntax and remove misplaced globals. No caller, credentials,
schedules, engine/action pins or consumer policy changes are proposed here.

Local schema-only candidates: home-ops (three files). These are NOT authorized
implementation tasks. Any request to apply
them needs owner approval identifying repository and files before dispatch.

Coverage gate: investigate execution paths only within the filtered scope.
Excluded forks, private and archived repositories create no migration tasks or
runner-coverage blockers. No owner exception is implied for unresolved coverage
inside the retained scope. Consumer edits still need separate authorization.

[argocd-secret-replacer]: https://github.com/mmalyska/argocd-secret-replacer/tree/3f2eb686dd0829f7f512e1a90983cde20795573b
[claude-plugins]: https://github.com/mmalyska/claude-plugins/tree/69eaec0368fa19d35f43c36a6551541922717110
[containers]: https://github.com/mmalyska/containers/tree/c474bfd3d6e77681b5c55cf9d267aeeb6fdebcfa
[dotfiles]: https://github.com/mmalyska/dotfiles/tree/bc0f69ff7b9221a334410a0ce0e3b46d45769918
[github-workflows]: https://github.com/mmalyska/github-workflows/tree/e046fef88b58a417fa53fa7989715cd5c02306d8
[helm-charts]: https://github.com/mmalyska/helm-charts/tree/519b54f016063a14f421173774c7ec7b5bd75352
[home-ops]: https://github.com/mmalyska/home-ops/tree/e734af0259ebd2208b742d43a1a0e9f60e4d1e4e
[homelab-portal]: https://github.com/mmalyska/homelab-portal/tree/42fcbee9335e961ddafc56816c551f80bfb9a3da
[renovate-config]: https://github.com/mmalyska/renovate-config/tree/b21cce1edd2e3a2ca1a86d99dc9881d21ce0d5ad
[vintagestory-mods-downloader]: https://github.com/mmalyska/vintagestory-mods-downloader/tree/5fa3f995aed172f579690a0ff691046ea71ba960
