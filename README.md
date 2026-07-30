# paseo-relay-builder

Automated multi-architecture container builds of the official
[Paseo Relay](https://github.com/getpaseo/paseo-relay).

This repository contains no forked relay source. GitHub Actions checks out the
official upstream `main` branch, builds its production `Dockerfile`, and
publishes the result to:

```text
ghcr.io/meixg/paseo-relay
```

## Deploy

### Relay only

The default example runs only Paseo Relay and binds it to the host loopback
interface:

```sh
cp compose.example.yml compose.yml
docker compose up -d
```

The relay is available to a reverse proxy on the same host at:

```text
http://127.0.0.1:4000
```

Terminate public TLS with nginx, Caddy, or another WebSocket-capable reverse
proxy. Port 4000 is not exposed on the host's public interfaces.

### Relay with Cloudflare Tunnel

Use the Cloudflare-specific example when Cloudflare Tunnel should provide the
public ingress:

```sh
cp compose.cloudflare-tunnel.example.yml compose.yml
export CLOUDFLARE_TUNNEL_TOKEN="<your-tunnel-token>"
docker compose up -d
```

In the Cloudflare Tunnel dashboard, route the public hostname (for example,
`relay.example.com`) to:

```text
http://relay:4000
```

This variant does not publish port 4000 on the host. The relay and cloudflared
communicate over a private Compose network.

### Image version

Both examples use the rolling image by default:

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
