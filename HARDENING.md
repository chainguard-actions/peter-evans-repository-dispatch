<!-- markdownlint-disable -->

# Hardening Report: peter-evans--repository-dispatch/v4.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **peter-evans--repository-dispatch/v4.0.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference Actions using mutable version tags instead of pinned 40-character SHA commits, making them vulnerable to supply-chain attacks if the tag is moved. Failing references include: actions/checkout@v5, actions/setup-node@v6, actions/upload-artifact@v5, actions/download-artifact@v6, peter-evans/create-pull-request@v7 (ci.yml); peter-evans/enable-pull-request-automerge@v3 (automerge-dependabot.yml); actions/checkout@v5 (on-repository-dispatch.yml); peter-evans/slash-command-dispatch@v4 (slash-command-dispatch.yml); peter-evans/repository-dispatch@v4 (test-dispatch.yml); actions/checkout@v5 (update-major-version.yml).

Locations:

- `.github/workflows/ci.yml:20`
- `.github/workflows/ci.yml:21`
- `.github/workflows/ci.yml:28`
- `.github/workflows/ci.yml:30`
- `.github/workflows/ci.yml:44`
- `.github/workflows/ci.yml:47`
- `.github/workflows/ci.yml:57`
- `.github/workflows/ci.yml:63`
- `.github/workflows/ci.yml:72`
- `.github/workflows/automerge-dependabot.yml:8`
- `.github/workflows/on-repository-dispatch.yml:12`
- `.github/workflows/slash-command-dispatch.yml:9`
- `.github/workflows/test-dispatch.yml:12`
- `.github/workflows/update-major-version.yml:18`

### script-injection (severity: high)

Sub-rule (a): GitHub Actions expressions are interpolated directly inside run: shell commands, allowing an attacker to inject arbitrary shell commands. (1) on-repository-dispatch.yml: `run: echo ${{ github.event.client_payload.sha }}` — the client_payload.sha value comes from an external repository_dispatch event and is interpolated directly into the shell command. (2) update-major-version.yml: `run: git tag -f ${{ github.event.inputs.main_version }} ${{ github.event.inputs.target }}` and `run: git push origin ${{ github.event.inputs.main_version }} --force` — workflow_dispatch inputs are interpolated directly into shell commands.

Locations:

- `.github/workflows/on-repository-dispatch.yml:17`
- `.github/workflows/update-major-version.yml:24`
- `.github/workflows/update-major-version.yml:26`

### missing-permissions (severity: medium)

The following workflow files have no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, workflows inherit the default repository permissions (which may be broad), violating the principle of least privilege. Affected files: automerge-dependabot.yml, on-repository-dispatch.yml, slash-command-dispatch.yml, update-major-version.yml.

Locations:

- `.github/workflows/automerge-dependabot.yml:1`
- `.github/workflows/on-repository-dispatch.yml:1`
- `.github/workflows/slash-command-dispatch.yml:1`
- `.github/workflows/update-major-version.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

Fixed all three findings across 6 workflow files: (1) Pinned all 8 action references to full 40-char commit SHAs with tag comments preserved; (2) Moved all GitHub expression interpolations in run: blocks to env: blocks to prevent script injection — on-repository-dispatch.yml (client_payload.sha) and update-major-version.yml (inputs.main_version and inputs.target); (3) Added minimal top-level permissions blocks to automerge-dependabot.yml (pull-requests: write, contents: write), on-repository-dispatch.yml (contents: read), slash-command-dispatch.yml (contents: read), and update-major-version.yml (contents: write).

