<!-- markdownlint-disable -->

# Hardening Report: hashicorp--vault-action/v3.4.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **hashicorp--vault-action/v3.4.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### hardcoded-credentials (severity: high)

Literal hardcoded token values are used in workflow steps. In `.github/workflows/build.yml`, the Vault token `testtoken` is hardcoded directly in six `with: token:` fields across the e2e test steps (e.g., `token: testtoken`). In `.github/workflows/local-test.yaml`, a literal `github-token: "foobar"` is passed to the `actions/github-script` step. Neither value uses a `${{ secrets.* }}` expression — they are plain-text credentials committed to the repository.

Locations:

- `.github/workflows/build.yml:109`
- `.github/workflows/build.yml:120`
- `.github/workflows/build.yml:131`
- `.github/workflows/build.yml:145`
- `.github/workflows/build.yml:156`
- `.github/workflows/build.yml:167`
- `.github/workflows/local-test.yaml:55`

### unpinned-uses (severity: high)

Two workflow files reference mutable, non-SHA-pinned action/image refs:
1. `.github/workflows/actionlint.yaml` uses `docker://docker.mirror.hashicorp.services/rhysd/actionlint:latest` — the `:latest` tag is mutable and can be silently replaced with a malicious image.
2. `.github/workflows/jira.yaml` uses `hashicorp/vault-workflows-common/.github/workflows/jira.yaml@main` — the `@main` branch ref is mutable and can point to any future commit.

Locations:

- `.github/workflows/actionlint.yaml:12`
- `.github/workflows/jira.yaml:9`

### missing-permissions (severity: medium)

None of the four workflow files define a `permissions:` block at the top level or at the job level. Without explicit permission scoping, workflows run with the default token permissions (which may include `contents: write` and other broad scopes depending on repository settings), violating the principle of least privilege. Affected files: build.yml, actionlint.yaml, jira.yaml, local-test.yaml.

Locations:

- `.github/workflows/build.yml:1`
- `.github/workflows/actionlint.yaml:1`
- `.github/workflows/jira.yaml:1`
- `.github/workflows/local-test.yaml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** hardcoded-credentials, unpinned-uses, missing-permissions

**Notes:**

Fixed all three findings across four workflow files:

1. hardcoded-credentials: Replaced 6x `token: testtoken` in build.yml with `${{ secrets.VAULT_TOKEN }}`, and replaced `github-token: "foobar"` in local-test.yaml with `${{ secrets.GITHUB_TOKEN }}`.

2. unpinned-uses: Pinned actionlint container image in actionlint.yaml to digest `sha256:b1934ee5f1c509618f2508e6eb47ee0d3520686341fec936f3b79331f9315667` (keeping :latest tag inline per convention). Pinned jira.yaml reusable workflow from `@main` to `@623b7b83929fa8b7779ffc56e6d2ad40c415f6d1 # main`.

3. missing-permissions: Added `permissions: {}` top-level block to all four workflow files (build.yml, actionlint.yaml, jira.yaml, local-test.yaml).

