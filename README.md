# Ansible role: DigitalOcean Cloud Firewall

Attach or detach an existing DigitalOcean Droplet to an existing DigitalOcean
Cloud Firewall.

## Table of Contents

- [Overview](#overview)
- [Requirements](#requirements)
- [Installation](#installation)
- [Variables](#variables)
- [Exported facts](#exported-facts)
- [Examples](#examples)
- [Testing](#testing)
- [Development notes](#development-notes)
- [Continuous integration](#continuous-integration)
- [Release workflow](#release-workflow)
- [Repository guidance](#repository-guidance)
- [Maintainer](#maintainer)
- [Support](#support)
- [Repository](#repository)
- [License](#license)

## Overview

The role is intentionally narrow in scope:

- it does not create or manage firewall rules
- it resolves one existing Cloud Firewall by name or ID
- it attaches or detaches one existing Droplet by ID
- it verifies the final firewall membership after the operation

Use this when another workflow already creates the Droplet and the firewall
definition is managed separately.

## Requirements

- Ansible Core 2.13 or newer
- a DigitalOcean API token with permission to read and update Cloud Firewalls
- an existing DigitalOcean Cloud Firewall
- an existing DigitalOcean Droplet ID

## Installation

### Current repository role names

- Local development role name in this repository:
  `digitalocean_cloud_firewall`
- Intended Ansible Galaxy FQCN from the current repository metadata:
  `inviqa.digitalocean_cloud_firewall`

For generic downstream playbook examples, this README uses the simple role name
`digitalocean_cloud_firewall`.

If you install the role under a different local name or through a namespace,
use the role name or FQCN that matches your own installation method.

### Publication status

The repository is being prepared for publication. Until that release exists,
it may still be consumed from a local checkout during migration work.

## Variables

| Variable | Default | Notes |
| --- | --- | --- |
| `digitalocean_cloud_firewall_api_base_url` | `https://api.digitalocean.com/v2` | Base DigitalOcean API URL. |
| `digitalocean_cloud_firewall_api_token` | `{{ lookup('env', 'DO_OAUTH_TOKEN') }}` | API token used for DigitalOcean API requests. |
| `digitalocean_cloud_firewall_droplet_id` | `""` | Target numeric Droplet identifier. |
| `digitalocean_cloud_firewall_name` | `""` | Firewall name to resolve when no explicit ID is set. |
| `digitalocean_cloud_firewall_id` | `""` | Firewall ID to use directly. Takes precedence over name. |
| `digitalocean_cloud_firewall_state` | `present` | Use `present` to attach the Droplet, or `absent` to detach it. |
| `digitalocean_cloud_firewall_timeout` | `60` | Timeout in seconds for DigitalOcean API requests. |

## Exported facts

The role exposes these facts:

- `digitalocean_cloud_firewall_resource`
- `digitalocean_cloud_firewall_droplet_ids`

## Examples

Attach a Droplet to a Cloud Firewall by name:

```yaml
---
- name: Attach Droplet to a firewall
  hosts: localhost
  gather_facts: false
  roles:
    - role: digitalocean_cloud_firewall
      vars:
        digitalocean_cloud_firewall_droplet_id: "123456789"
        digitalocean_cloud_firewall_name: gateway
```

Detach a Droplet from a Cloud Firewall by ID:

```yaml
---
- name: Detach Droplet from a firewall
  hosts: localhost
  gather_facts: false
  roles:
    - role: digitalocean_cloud_firewall
      vars:
        digitalocean_cloud_firewall_droplet_id: "123456789"
        digitalocean_cloud_firewall_id: "11111111-2222-3333-4444-555555555555"
        digitalocean_cloud_firewall_state: absent
```

If you install the published role under its current namespace, replace
`digitalocean_cloud_firewall` with `inviqa.digitalocean_cloud_firewall`.

## Testing

The current test workflow is documented in [docs/testing.md](docs/testing.md).
It covers Workspace commands, DigitalOcean live tests, Jenkinsfile lint,
cleanup, optional DigitalOcean project assignment and SSH key injection for
test Droplets, the Workspace CLI install command, and manual playbook runs
through `ws ansible playbook`.

## Development notes

- The role uses DigitalOcean's REST API directly for additive membership
  changes so it does not need to own the complete firewall rule definition.
- `tasks/main.yml` is only the role orchestrator; validation, firewall lookup,
  membership changes, refresh, and verification live in focused task files.
- The role expects callers to pass project-specific firewall names or IDs from
  inventory or group variables.
- `workspace.yml` provides the preferred local test and release command
  surface: `ws ansible lint`, `ws ansible syntax`,
  `ws ansible playbook <playbook> <inventory>`,
  `ws test-live provision|cleanup|full-cycle`, and the release preflight
  commands documented below.
- The repo is being prepared for Galaxy publication, so the metadata and
  documentation are intentionally kept explicit.

## Continuous integration

- `Jenkinsfile` defines the private Jenkins CI entrypoint for this role.
- [docs/jenkins-ci.md](docs/jenkins-ci.md) documents the Jenkins build
  parameters, where maintainers set them with **Build with Parameters**,
  required credentials, Workspace environment, validation stages, live
  DigitalOcean test stage, release preflight, and cleanup behavior.

## Release workflow

- [docs/ansible-galaxy-release.md](docs/ansible-galaxy-release.md) documents
  the GitHub release and Ansible Galaxy import flow.
- Workspace commands keep local and Jenkins release behavior aligned:
  `ws github release check`, `ws github release publish`,
  `ws ansible galaxy check-token`, `ws ansible galaxy info`, and
  `ws ansible galaxy publish`.
- Galaxy publishing reads `ansible.galaxy.token` from
  `workspace.override.yml` or `ANSIBLE_GALAXY_TOKEN`; Jenkins uses the
  `ansible-roles-galaxy-token` Secret text credential.

## Repository guidance

- `AGENTS.md` defines repository-specific editing, linting, and documentation
  expectations for AI coding agents working in this role.
- `.ansible/` is treated as generated or vendored content and should not be
  hand-edited; update the repository source files instead.
- `workspace.override.yml` and `tests/test_variables.yml` are local
  secret-bearing override files and must stay untracked.

## Maintainer

- Maintainer: Marco Massari Calderone `<marco.massari-calder@inviqa.com>`
- Copyright holder: Inviqa UK Ltd

## Support

- For current maintenance and publication work, contact the maintainer above.
- If and when the role is published publicly, issue-tracking and support paths
  should be documented alongside the published source.

## Repository

- Repository URL: <https://github.com/inviqa/ansible-digitalocean-cloud-firewall>
- Publication status: pending Ansible Galaxy publication

## License

MIT
