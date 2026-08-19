<!-- markdownlint-disable -->

# Hardening Report: hashicorp--vault-action/v4.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **hashicorp--vault-action/v4.0.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### hardcoded-credentials (severity: high)

Hardcoded literal token values found in workflow files. In local-test.yaml, `github-token: "foobar"` is a hardcoded non-expression token value (line 49), and `token: testtoken` is a hardcoded Vault token (line 43). In build.yml, `token: testtoken` appears six times as a hardcoded Vault token passed to the action under test (lines ~100, 108, 116, 127, 135, 141). These literal credential values should be replaced with secret expressions like `${{ secrets.VAULT_TOKEN }}`.

Locations:

- `.github/workflows/local-test.yaml:43`
- `.github/workflows/local-test.yaml:49`
- `.github/workflows/build.yml:100`

### unpinned-uses (severity: high)

Unpinned action/image references found: (1) jira.yaml uses `hashicorp/vault-workflows-common/.github/workflows/jira.yaml@main` — a mutable branch ref, not a full 40-character commit SHA. (2) actionlint.yaml uses `docker://docker.mirror.hashicorp.services/rhysd/actionlint:latest` — a mutable `:latest` tag instead of a SHA digest. Both are vulnerable to supply-chain attacks if the referenced content changes.

Locations:

- `.github/workflows/jira.yaml:11`
- `.github/workflows/actionlint.yaml:10`

### missing-permissions (severity: medium)

None of the workflow files define a top-level `permissions:` block, and no job within any workflow defines job-level `permissions:`. This means all jobs run with the default token permissions (read/write to most resources), violating the principle of least privilege. Affected files: build.yml, local-test.yaml, jira.yaml, actionlint.yaml.

Locations:

- `.github/workflows/build.yml:1`
- `.github/workflows/local-test.yaml:1`
- `.github/workflows/jira.yaml:1`
- `.github/workflows/actionlint.yaml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** hardcoded-credentials, unpinned-uses, missing-permissions

**Notes:**

Fixed all three findings across four workflow files:

1. hardcoded-credentials: Replaced `token: testtoken` with `${{ secrets.VAULT_TOKEN }}` in local-test.yaml (line 43) and all 6 occurrences in build.yml (e2e job steps). Replaced `github-token: "foobar"` with `${{ secrets.GITHUB_TOKEN }}` in local-test.yaml (line 49).

2. unpinned-uses: Pinned jira.yaml's `hashicorp/vault-workflows-common/.github/workflows/jira.yaml@main` to full SHA `623b7b83929fa8b7779ffc56e6d2ad40c415f6d1` (with `# main` comment). Pinned actionlint.yaml's `docker://docker.mirror.hashicorp.services/rhysd/actionlint:latest` to `docker://docker.mirror.hashicorp.services/rhysd/actionlint:latest@sha256:b1934ee5f1c509618f2508e6eb47ee0d3520686341fec936f3b79331f9315667` (preserving docker:// scheme and :latest tag inline).

3. missing-permissions: Added `permissions: {}` top-level block to build.yml, local-test.yaml, jira.yaml, and actionlint.yaml.

