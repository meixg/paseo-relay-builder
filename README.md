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

## Deploy

Copy the included example:

```sh
cp compose.example.yml compose.yml
export CLOUDFLARE_TUNNEL_TOKEN="<your-tunnel-token>"
docker compose up -d
```

In the Cloudflare Tunnel dashboard, route the public hostname (for example,
`relay.example.com`) to:

```text
http://relay:4000
```

The example does not publish port 4000 on the host. Both services communicate
over a private Compose network, and remote traffic enters through Cloudflare
Tunnel.

It uses the rolling image by default:

```dotenv
PASEO_RELAY_IMAGE=ghcr.io/meixg/paseo-relay:latest
```

For predictable production rollouts, set an immutable tag before starting:

```sh
export PASEO_RELAY_IMAGE="ghcr.io/meixg/paseo-relay:sha-<12 characters>"
docker compose up -d
```

The current immutable tag is shown in each successful workflow summary.

## Trust model

The image is built from the official upstream source at the revision recorded
in its OCI metadata. GitHub Actions dependencies are monitored by Dependabot.
Review dependency updates and upstream changes before merging or deploying
them.
