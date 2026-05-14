# Changelog

All notable changes to this project will be documented in this file.

## 0.1.0 - 2026-05-15

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
- Standalone live DigitalOcean test harness that creates temporary resources,
  validates attach and detach behavior, and provides a cleanup playbook.
- Role-local lint configuration for YAML and Ansible validation.
- MIT license file.
- Jenkins CI pipeline for dependency installation, syntax checks, optional live
  DigitalOcean tests, cleanup, and Slack failure notification, using the shared
  `digitalocean-ansible-roles-oauth-token` credential.
- Repository-specific `AGENTS.md` guidance for linting, documentation,
  changelog, and live-test handling.
