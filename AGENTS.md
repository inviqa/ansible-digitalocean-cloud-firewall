# AGENTS.md

Follow `~/AGENTS.md` for the canonical user-wide policy. This file applies to
the repository root and is mandatory for agents editing this role.

## Fast Start

- Read `workspace.yml`, `docs/testing.md`, `docs/jenkins-ci.md`, and
  `docs/ansible-galaxy-release.md` before changing test, Jenkins, release, or
  Workspace behavior.
- Treat `.ansible/` as generated dependency/cache output. Edit role source
  files in the repository root instead.
- Keep real credentials only in ignored local files:
  `workspace.override.yml` for Workspace commands and `tests/test_variables.yml`
  for direct Ansible runs. Examples and docs must use placeholders only.
- This Cloud Firewall harness uses only the DigitalOcean API. It does not SSH
  into test Droplets, so do not add SSH key or SSH agent requirements unless the
  live test starts making SSH connections.

## Required Checks

Run the relevant checks after each meaningful change set and before handoff.
Fix findings instead of suppressing them unless an exception is explicitly
approved and documented.

| Changed files | Required checks |
| --- | --- |
| `*.sh` or shell shebang scripts | `shellcheck --enable=all <file>` |
| `*.yml`, `*.yaml` | `yamllint <file>` |
| Ansible role, playbook, vars, defaults, metadata, or `tests/**/*.yml` files | `yamllint <file>` and `ansible-lint .` |
| `*.md` | `markdownlint` with the global config from `~/AGENTS.md` |
| `*.py` | `ruff check <file>` |
| `Jenkinsfile` or Jenkins helper files | `ws lint-jenkinsfile` plus file-type checks |
| `workspace.yml`, live-test playbooks, or role task flow | `ws ansible syntax` and `ansible-lint .` |

If a required linter is unavailable, report that clearly and include the exact
install command.

## Repository Contracts

- Update `CHANGELOG.md` whenever code, behavior, or documentation changes. If
  an `Unreleased` section exists, use it; otherwise update the current release
  entry only when that release has not been published yet.
- Update the root `README.md` whenever repository documentation is added,
  renamed, moved, or deleted. Keep its table of contents and maintainer,
  support, publication, and installation details aligned.
- Keep shell automation compatible with both macOS and Linux Bash. Do not
  embed Python snippets inside Bash scripts or Bash command strings, and avoid
  GNU-only flags unless the dependency is already documented.
- Do not commit user-specific absolute filesystem paths. Use
  repository-relative paths, or `~` only when a home-relative path is genuinely
  required.
- Keep Jenkins operator choices as per-build controls, not fixed credential
  environment values. Live-test enablement/target, release version selection,
  and GitHub/Galaxy publication gates belong in Jenkins parameters or an
  equivalent explicit input surface.
- When changing Jenkinsfile publication or live-test behavior, keep
  `docs/jenkins-ci.md`, `docs/ansible-galaxy-release.md`, and `README.md`
  aligned with the real split between Jenkins parameters, credential bindings,
  and Workspace commands.
- When renaming externally created live-test resources, keep cleanup compatible
  with previous names long enough to remove resources left by interrupted older
  runs.
- Add cloud quota or allowance preflights only after current-resource
  discovery, and gate only the creation path so idempotent reruns do not fail
  when the account is already at quota.
- Test rescue blocks must re-raise or fail after logging unless the recovered
  state is intentionally acceptable and documented in the task.
- When parsing provider metadata booleans, compare normalized expected values
  instead of relying on broad truthiness filters for arbitrary strings.
- If future work edits host network configuration, replace only the route or
  setting owned by this role and preserve unrelated existing entries.
- Use `include_tasks` instead of `import_tasks` when the included task file
  contains `ansible.builtin.meta` tasks such as `reset_connection` and the
  include site has a `when` condition.

## Suggested Commands

```text
shellcheck --enable=all tests/lint_jenkinsfile.sh
yamllint workspace.yml workspace.override.yml.example tests/playbook.yml tests/playbook_cleanup.yml
ansible-lint .
markdownlint -c ~/.markdownlint.json AGENTS.md README.md CHANGELOG.md docs/*.md tests/README.md
ws ansible syntax
ws lint-jenkinsfile
```
