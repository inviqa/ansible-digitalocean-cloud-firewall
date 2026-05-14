# Test Harness

This directory contains the live DigitalOcean integration tests for the role.
Run the commands from the role root, not from a downstream project that vendors
the role.

## Contents

- [Coverage](#coverage)
- [Setup](#setup)
- [Run the tests](#run-the-tests)
- [Clean up](#clean-up)
- [Notes](#notes)

## Coverage

The test harness creates:

- one temporary DigitalOcean Droplet
- one temporary DigitalOcean Cloud Firewall

It then exercises the role twice:

- `present` attaches the Droplet to the firewall and verifies membership
- `absent` detaches the Droplet from the firewall and verifies removal

## Setup

Install the required collection from the role root:

```bash
ansible-galaxy collection install -r tests/requirements.yml
```

Copy the example variables file and edit it:

```bash
cp tests/test_variables.example.yml tests/test_variables.yml
```

Set your DigitalOcean API token:

```yaml
digitalocean_cloud_firewall_test_api_token: "dop_v1_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
```

If `DIGITAL_OCEAN_API_TOKEN` or `DO_OAUTH_TOKEN` is exported in the current
shell, it takes precedence over `digitalocean_cloud_firewall_test_api_token` in `test_variables.yml`.

## Run the tests

From the role root:

```bash
ansible-playbook -i tests/inventory tests/playbook.yml
```

## Clean up

Destroy the test Droplet and Cloud Firewall:

```bash
ansible-playbook -i tests/inventory tests/playbook_cleanup.yml
```

Run cleanup after failed or interrupted test runs before retrying.

## Notes

- The harness provisions real DigitalOcean resources, so it incurs cost.
- No AWS credentials are required; only DigitalOcean credentials are used.
- The harness does not SSH into the test Droplet.
- Test resources are named
  `ansible-digitalocean-cloud-firewall-<inventory-name>` and tagged with
  `ANSIBLE-TEST`.
- If you get a `401 Unauthorized` error, verify `digitalocean_cloud_firewall_test_api_token` in
  `tests/test_variables.yml` or export a valid `DIGITAL_OCEAN_API_TOKEN` /
  `DO_OAUTH_TOKEN`.
