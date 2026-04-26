# Shared GitHub Automation

This repository contains reusable workflows and composite actions for the
`cesaregarza/*` repositories.

Secrets intentionally stay in the caller repositories or environments. Shared
workflows declare the secrets they need, and callers pass them explicitly.

## Reusable Workflows

- `.github/workflows/broker-smoke-test.yml` - test the GitHub OIDC broker path
  with a fake credential.
- `.github/workflows/claude.yml` - standard Claude Code mention workflow.
- `.github/workflows/claude-code-review.yml` - standard Claude PR review workflow.
- `.github/workflows/python-uv-ci.yml` - small uv + pytest CI workflow.
- `.github/workflows/update-splattop-config-images.yml` - update image tags in
  `cesaregarza/SplatTopConfig` and open a PR.

## Composite Actions

- `actions/fetch-broker-credentials` - request a GitHub OIDC token, call the
  credential broker, mask returned values, and optionally export them to later
  shell steps.
- `actions/compute-next-version` - read `.version`, inspect the latest GitHub
  release, and output the next patch version.
- `actions/setup-docr-buildx` - configure Docker Buildx and log in to
  DigitalOcean Container Registry.
- `actions/build-docr-image` - build and optionally push one Docker image with
  GHA caching.

## Caller Pattern

```yaml
jobs:
  claude:
    uses: cesaregarza/.github/.github/workflows/claude.yml@main
    secrets:
      CLAUDE_CODE_OAUTH_TOKEN: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}
```
