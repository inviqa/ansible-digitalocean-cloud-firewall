# Changelog

All notable changes to this project will be documented in this file.

## [0.1.0] - 2026-05-20

### Added

- Initial `digitalocean_cloud_firewall` role for attaching or detaching one
  existing DigitalOcean Droplet to an existing DigitalOcean Cloud Firewall.
- Firewall lookup by explicit firewall ID or by firewall name, with validation
  that the lookup resolves to exactly one firewall.
- Idempotent membership handling for `present` and `absent` states, avoiding
  unnecessary attach or detach API calls when the Droplet membership already
  matches the requested state.
- Post-change membership refresh and final assertions to verify that
  DigitalOcean applied the requested firewall membership.
- Exported facts for the resolved firewall resource and current Droplet
  membership list.
- Configurable DigitalOcean API base URL, API token, Droplet ID, firewall ID,
  firewall name, desired state, and request timeout.
- Focused task layout with `tasks/main.yml` as the role orchestrator.
- Galaxy-ready role metadata for the intended
  `inviqa.digitalocean_cloud_firewall` namespace.
- Workspace-managed local command surface for role linting, Ansible syntax checks, live
  DigitalOcean tests, Jenkinsfile linting, GitHub release checks, GitHub
  publication, Ansible Galaxy token checks, Galaxy status, and Galaxy import.
- Namespaced Ansible helper commands under `ws ansible lint`,
  `ws ansible syntax`, `ws ansible playbook`, and
  `ws ansible galaxy <action>` subcommands, with grouped Workspace usage help
  for Ansible, Galaxy, config, GitHub, global, and secret command groups.
- Non-interactive `ws console <command>` rejects quoted shell snippets instead
  of corrupting them; use an interactive `ws console` shell or a dedicated
  Workspace command for shell-quoted commands.
- Containerized Ansible commands keep Ansible home and role cache paths inside
  the container so host-created `.ansible/` cache links do not break Workspace
  validation.
- Testing and release documentation for `ws ansible playbook`,
  `ws test-live provision|cleanup|full-cycle`, and nested GitHub/Galaxy release
  actions.
- Docker Compose environment and Dockerized Jenkins Declarative Pipeline lint
  helper for repeatable local `Jenkinsfile` validation without SSH agent
  mounts or unused Jenkins SSH plugins.
- Standalone live DigitalOcean test harness that creates temporary resources,
  optionally assigns the test Droplet to a configured DigitalOcean project,
  validates attach and detach behavior without SSH access, and provides a
  cleanup playbook.
- Live-test configuration through preferred `workspace.override.yml`
  attributes for Workspace commands, placeholder-only example values, and the
  gitignored `tests/test_variables.yml` file for direct Ansible execution.
- Live-test failure reporting that still calls cleanup and makes cleanup
  failures visible when the main live test also fails.
- Live-test Workspace phases for `provision`, `cleanup`, and `full-cycle`, with
  Jenkins using the safe full-cycle command plus an idempotent cleanup safety
  net.
- True Workspace subcommands for `ws test-live provision`,
  `ws test-live cleanup`, and `ws test-live full-cycle`.
- Targetless Cloud Firewall live-test commands because this harness creates one
  Droplet and one Cloud Firewall instead of a target matrix.
- Direct live-test subcommand scripts without one-off Bash helper functions.
- Testing, Jenkins CI, and Ansible Galaxy release documentation under `docs/`,
  with README links to the maintained workflows.
- Testing documentation listing the Workspace override attributes used by live
  tests and release commands.
- Shorter, phase-oriented Mermaid flowcharts in the testing, Jenkins CI, and
  Ansible Galaxy release documentation.
- Jenkins CI pipeline that runs Workspace linting, syntax checks, release
  preflight, optional live DigitalOcean tests, optional GitHub release
  publication, optional Ansible Galaxy import, cleanup, and Slack failure
  notification.
- Repository-specific `AGENTS.md` guidance for linting, documentation,
  changelog, Jenkins, Workspace, and live-test handling.
- Credential validation keeps non-secret setup guidance visible while keeping
  token-bearing API checks hidden from logs.
- Role-local lint configuration for YAML and Ansible validation.
- MIT license file.
