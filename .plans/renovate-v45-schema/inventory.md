# Repository assessment

## Owner scope clarification

The owner excluded archived repositories because they are no longer used
(Discord message 1575870273436000428). They require no migration, validation,
runner coverage, or approval gate in this project. This clarification narrows
the archived-case disposition in spec.md; all other requirements remain intact.

## Method and limits

Read-only snapshot on 2026-11-27: paginated `users/mmalyska/repos` (28 public
repositories), cross-checked against authenticated `user/repos` (10 visible
owner repositories, all already included). Default-branch recursive trees were
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
All 17 initially identified consumer configuration files (including the
subsequently
excluded archived talos-images file) returned exit 0. This validates
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

## Independent configuration

The following files do not extend mmalyska/renovate-config. No direct Renovate
execution workflow was established by reviewing their workflows: Renovate text
in ordinary CI is not a runner. Hosted integration/external execution and
effective
onboarding/requireConfig values remain UNKNOWN, including whether execution is
active at all. Their independent policies must not be attached to the shared
preset automatically. Each standalone file validates with exit 0 and no
migration
warning on 45.1.1; resolved external integration validation is NOT RUN because
its execution configuration is unavailable.

- siderolabs-extensions: .github/renovate.json; built-in
  dashboard/sign-off/scope
  and early-Monday presets; generated regex managers, dependency group, file
  exclusions and versioning overrides. Disposition: independent, runner unknown.
- siderolabs-pkgs: .github/renovate.json; similar built-ins, generated Pkgfile
  managers and package-specific versioning/limits. Disposition: runner unknown.
- talos: .github/renovate.json; generated built-ins with non-office-hour
  schedule,
  custom managers, grouped dependencies and draft PR policy. Runner unknown.
- talos-jetson-orin: renovate.json; config:recommended and dashboard; always-on
  schedule with explicit limits, package-specific automerge and manual Talos
  acceptance constraints. Runner unknown; intentional policy difference.

## Complete repository ledger

Each source link refers to the inspected default-branch commit. "No identified
config/runner" means no discovery hit, not proof that no external integration
exists. Archived repositories are retained only as excluded discovery entries;
they
are not audited consumers or blockers.

- [action-setup-kube-tools]: no identified config/runner; not archived;
  disabled=false.
- [argo-cd]: no identified config/runner; not archived; disabled=false.
- [argocd-secret-replacer]: shared-policy consumer; not archived;
  disabled=false.
- [CalkaGPU]: excluded from scope (archived; unused).
- [claude-plugins]: no identified config/runner (plugin manifests are not bot
  configuration); not archived; disabled=false.
- [containers]: shared-policy consumer; not archived; disabled=false.
- [docker-compose-elk]: excluded from scope (archived; unused).
- [dotfiles]: shared-policy consumer; not archived; disabled=false.
- [Enigma]: excluded from scope (archived; unused).
- [EnigmaSim]: excluded from scope (archived; unused).
- [factorio-server-charts]: no identified config/runner; not archived;
  disabled=false.
- [github-workflows]: shared-policy consumer; not archived; disabled=false.
- [helm-charts]: no identified config/runner; not archived; disabled=false.
- [home-assistant]: excluded from scope (archived; unused).
- [home-ops]: shared-policy consumer; not archived; disabled=false.
- [homelab-portal]: shared-policy consumer; not archived; disabled=false.
- [katacoda-scenarios]: excluded from scope (archived; unused).
- [KF2-Server-Script]: excluded from scope (archived; unused).
- [modorganizer]: no identified config/runner; not archived; disabled=false.
- [renovate-config]: shared preset publisher; not archived; disabled=false.
- [siderolabs-extensions]: independent configuration; runner unknown; not
  archived; disabled=false.
- [siderolabs-pkgs]: independent configuration; runner unknown; not archived;
  disabled=false.
- [talos]: independent configuration; runner unknown; not archived;
  disabled=false.
- [talos-images]: excluded from scope (archived; unused).
- [talos-jetson-orin]: independent configuration; runner unknown; not archived;
  disabled=false.
- [vintagestory-mods-downloader]: no identified config/runner; not archived;
  disabled=false.
- [willow-inference-server-dockerized]: excluded from scope (archived; unused).
- [YubiKey-Guide]: no identified config/runner; not archived; disabled=false.

## Proposed changes versus decisions

Authorized implementation scope remains github-workflows and renovate-config
only: add explicit workflow globals, verify their propagation, then migrate the
shared preset syntax and remove misplaced globals. No caller, credentials,
schedules, engine/action pins or consumer policy changes are proposed here.

Local schema-only candidates: home-ops (three files). These are NOT authorized
implementation tasks. Any request to apply
them needs owner approval identifying repository and files before dispatch.

Policy/coverage questions: independently configured repositories do not
currently
inherit the shared preset in their repository files; attaching it would be a
policy change, not migration. Missing external runner evidence must be resolved
or explicitly accepted by the owner before shared globals are removed. No owner
exception is implied by approving this plan. T1 rechecks whether hidden
execution
paths consume the shared preset, and stops on unresolved relevant coverage.

[action-setup-kube-tools]: https://github.com/mmalyska/action-setup-kube-tools/tree/5fe385031665158529decddddb51d6224422836e
[argo-cd]: https://github.com/mmalyska/argo-cd/tree/75313e0d57de8f048cb9517104f176d2662171ad
[argocd-secret-replacer]: https://github.com/mmalyska/argocd-secret-replacer/tree/3f2eb686dd0829f7f512e1a90983cde20795573b
[CalkaGPU]: https://github.com/mmalyska/CalkaGPU/tree/5482fe4238aab180a909c1fcbec65dbd0816287b
[claude-plugins]: https://github.com/mmalyska/claude-plugins/tree/69eaec0368fa19d35f43c36a6551541922717110
[containers]: https://github.com/mmalyska/containers/tree/c474bfd3d6e77681b5c55cf9d267aeeb6fdebcfa
[docker-compose-elk]: https://github.com/mmalyska/docker-compose-elk/tree/8c134ec6e3b1d0378d95637593ec368a4520795a
[dotfiles]: https://github.com/mmalyska/dotfiles/tree/bc0f69ff7b9221a334410a0ce0e3b46d45769918
[Enigma]: https://github.com/mmalyska/Enigma/tree/623c50eb657329bcadbb906d2fb80a5aeab76b31
[EnigmaSim]: https://github.com/mmalyska/EnigmaSim/tree/9fc0103af8f4bd1092662054aed03740a7953436
[factorio-server-charts]: https://github.com/mmalyska/factorio-server-charts/tree/a22ba078a18d61971ce1e5140702a9eee483ddbb
[github-workflows]: https://github.com/mmalyska/github-workflows/tree/e046fef88b58a417fa53fa7989715cd5c02306d8
[helm-charts]: https://github.com/mmalyska/helm-charts/tree/519b54f016063a14f421173774c7ec7b5bd75352
[home-assistant]: https://github.com/mmalyska/home-assistant/tree/7a1fb3f8f4960420fa97307dde57fcc3875ba776
[home-ops]: https://github.com/mmalyska/home-ops/tree/e734af0259ebd2208b742d43a1a0e9f60e4d1e4e
[homelab-portal]: https://github.com/mmalyska/homelab-portal/tree/42fcbee9335e961ddafc56816c551f80bfb9a3da
[katacoda-scenarios]: https://github.com/mmalyska/katacoda-scenarios/tree/828b7dfbc4aa31fa9341e9bfe6fac280209791c3
[KF2-Server-Script]: https://github.com/mmalyska/KF2-Server-Script/tree/d67731a98d6269472fb9d34b9e1d478da0f90219
[modorganizer]: https://github.com/mmalyska/modorganizer/tree/a931277b2b442548f0f0d15d75b37e139b4d0218
[renovate-config]: https://github.com/mmalyska/renovate-config/tree/b21cce1edd2e3a2ca1a86d99dc9881d21ce0d5ad
[siderolabs-extensions]: https://github.com/mmalyska/siderolabs-extensions/tree/5a0db5378c1f396448c1e339a8fd19e6c0326a8e
[siderolabs-pkgs]: https://github.com/mmalyska/siderolabs-pkgs/tree/3bef04361931a686d163a0c3cc76165f1059b838
[talos]: https://github.com/mmalyska/talos/tree/81afbe314abf615ed826e8ff604c5c23cb36bb3e
[talos-images]: https://github.com/mmalyska/talos-images/tree/443d9c08a174b14cf572a45b3af50c60b4fb173a
[talos-jetson-orin]: https://github.com/mmalyska/talos-jetson-orin/tree/23dbcb0ad46eaa64c511fcb219c0847e16ca0cc4
[vintagestory-mods-downloader]: https://github.com/mmalyska/vintagestory-mods-downloader/tree/5fa3f995aed172f579690a0ff691046ea71ba960
[willow-inference-server-dockerized]: https://github.com/mmalyska/willow-inference-server-dockerized/tree/f990b6ad3a71d65428ae16f4450b2a5cc11ce661
[YubiKey-Guide]: https://github.com/mmalyska/YubiKey-Guide/tree/5eeae2be7e988871c8db8a54f4ae7f393c9317ae
