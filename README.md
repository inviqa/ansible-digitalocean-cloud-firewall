# Ansible role: DigitalOcean Cloud Firewall

Attach or detach an existing DigitalOcean Droplet to an existing DigitalOcean
Cloud Firewall.

## Table of Contents

- [Overview](#overview)
- [Requirements](#requirements)
- [Variables](#variables)
- [Exported facts](#exported-facts)
- [Examples](#examples)
- [Testing](#testing)
- [Development notes](#development-notes)
- [Maintainer](#maintainer)
- [Support](#support)
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

## Variables

| Variable | Default | Notes |
| --- | --- | --- |
| `digitalocean_cloud_firewall_api_base_url` | `https://api.digitalocean.com/v2` | Base DigitalOcean API URL. |
| `digitalocean_cloud_firewall_api_token` | `{{ lookup('env', 'DO_OAUTH_TOKEN') }}` | API token used for DigitalOcean API requests. |
| `digitalocean_cloud_firewall_droplet_id` | `""` | Target Droplet identifier. |
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

## Testing

The role includes a live DigitalOcean test harness under `tests/`. Run it from
the role root so it can be moved out of this repository later without changing
the execution model:

```bash
ansible-galaxy collection install -r tests/requirements.yml
ansible-playbook -i tests/inventory tests/playbook.yml
ansible-playbook -i tests/inventory tests/playbook_cleanup.yml
```

The harness creates a temporary Droplet and Cloud Firewall, validates attach
and detach behavior, then removes both resources during cleanup.

The test harness resolves the role through `tests/roles/digitalocean_cloud_firewall`,
which is a relative symlink back to the role root. This keeps the live tests
independent from the checkout directory name.

## Development notes

- The role uses DigitalOcean's REST API directly for additive membership
  changes so it does not need to own the complete firewall rule definition.
- `tasks/main.yml` is only the role orchestrator; validation, firewall lookup,
  membership changes, refresh, and verification live in focused task files.
- The role expects callers to pass project-specific firewall names or IDs from
  inventory or group variables.
- If the role is published later, the intended Galaxy FQCN from current
  metadata is `inviqa.digitalocean_cloud_firewall`.

## Maintainer

Inviqa DevOps.

## Support

This role is maintained by Inviqa DevOps.

## License

MIT
