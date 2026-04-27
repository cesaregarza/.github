# Shared GitHub Automation

This repository contains reusable workflows and composite actions for the
`cesaregarza/*` repositories.

Secrets intentionally stay out of caller repositories and shared workflow
definitions. Caller jobs use GitHub OIDC to request narrow credential
capabilities from the credential broker.

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
  credential broker capability endpoint, mask returned values, and optionally
  export them to later shell steps.
- `actions/compute-next-version` - read `.version`, inspect the latest GitHub
  release, and output the next patch version.
- `actions/setup-docr-buildx` - configure Docker Buildx and log in to
  DigitalOcean Container Registry.
- `actions/build-docr-image` - build and optionally push one Docker image with
  GHA caching.

## Caller Pattern

Callers must grant `id-token: write`; otherwise GitHub will not expose the OIDC
request environment variables. Keep `secrets-json` inside the same job and do
not promote it to job outputs.

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      id-token: write
    steps:
      - uses: cesaregarza/.github/actions/fetch-broker-credentials@main
        with:
          broker-url: https://credentials.garz.ai
          audience: https://credentials.garz.ai/v1
          capability: digitalocean-k8s-deploy
          export-env: "true"
```
