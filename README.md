# paseo-relay-builder

Automated multi-architecture container builds of the official
[Paseo Relay](https://github.com/getpaseo/paseo-relay).

This repository contains no forked relay source. GitHub Actions checks out the
official upstream `main` branch, builds its production `Dockerfile`, and
publishes the result to:

```text
ghcr.io/meixg/paseo-relay
```

## Published tags

- `latest`: the newest upstream `main` revision built successfully.
- `sha-<12 characters>`: an immutable tag for a specific upstream commit.

Images are published for `linux/amd64` and `linux/arm64`.

The workflow runs every six hours and can also be started manually from the
Actions tab. Before building, it checks whether the immutable revision tag
already exists in GHCR, so unchanged upstream revisions are skipped.

## First-time setup

After the first successful workflow run, open the package settings on GitHub and
change the package visibility to **Public**. Public GHCR images can then be
pulled without registry credentials.

Public repositories with no repository activity for 60 days may have scheduled
workflows disabled by GitHub. If that happens, re-enable the workflow from the
Actions tab or trigger it manually.

## Deploy

For a rolling deployment:

```yaml
services:
  relay:
    image: ghcr.io/meixg/paseo-relay:latest
    restart: unless-stopped
    environment:
      PASEO_RELAY_HOST: 0.0.0.0
      PASEO_RELAY_PORT: 4000
      PASEO_RELAY_MIN_CLUSTER_SIZE: 1
      PASEO_RELAY_OWNERSHIP_TARGET: local
```

For predictable production rollouts, replace `latest` with the immutable
`sha-<12 characters>` tag shown in the workflow summary.

## Trust model

The image is built from the official upstream source at the revision recorded
in its OCI metadata. GitHub Actions dependencies are monitored by Dependabot.
Review dependency updates and upstream changes before merging or deploying
them.
