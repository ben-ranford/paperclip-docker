# Paperclip Docker Image

[![Nightly Publish](https://img.shields.io/github/actions/workflow/status/ben-ranford/paperclip-docker/upstream-sync-and-release.yml?label=nightly%20publish)](https://github.com/ben-ranford/paperclip-docker/actions/workflows/upstream-sync-and-release.yml)
[![GHCR](https://img.shields.io/badge/GHCR-ghcr.io%2Fben--ranford%2Fpaperclip-2ea44f?logo=github)](https://ghcr.io/ben-ranford/paperclip)

Quick start for running Paperclip via Docker.

Image:
- [ghcr.io/ben-ranford/paperclip:latest](https://ghcr.io/ben-ranford/paperclip)

Upstream app source:
- [paperclipai/paperclip](https://github.com/paperclipai/paperclip)

Patch details:
- [docs/patch.md](docs/patch.md)

## Quick Start

1. Pull the image.

```sh
docker pull ghcr.io/ben-ranford/paperclip:latest
```

2. Create a local data directory.

```sh
mkdir -p ./data/paperclip
```

3. Run Paperclip on port `3100`.

```sh
docker run --rm \
  -p 3100:3100 \
  -e BETTER_AUTH_SECRET=replace-with-strong-secret \
  -e PAPERCLIP_PUBLIC_URL=http://localhost:3100 \
  -v "$(pwd)/data/paperclip:/paperclip" \
  ghcr.io/ben-ranford/paperclip:latest
```

Open `http://localhost:3100` in your browser.

## Common Config

- `BETTER_AUTH_SECRET` (required): auth/session signing secret.
- `PAPERCLIP_PUBLIC_URL` (recommended): base URL used for links and callbacks.
- `DATABASE_URL` (optional): use external PostgreSQL when set; embedded PostgreSQL is used when unset.
- `OPENAI_API_KEY` (optional): enables Codex adapter.
- `ANTHROPIC_API_KEY` (optional): enables Claude adapter.
- `PAPERCLIP_ALLOW_AGENT_ASSIGN` (optional): set `true` to allow agents to assign/reassign issues without `tasks:assign`.
- `PAPERCLIP_FIX_OWNERSHIP` (default `1`): set `0` to skip startup ownership repair on `/paperclip`.
- `PAPERCLIP_RUNTIME_UID` and `PAPERCLIP_RUNTIME_GID` (default `1000`): runtime UID/GID used by the container entrypoint.

## External PostgreSQL Example

```sh
docker run --rm \
  -p 3100:3100 \
  -e BETTER_AUTH_SECRET=replace-with-strong-secret \
  -e PAPERCLIP_PUBLIC_URL=http://localhost:3100 \
  -e DATABASE_URL=postgres://paperclip:paperclip@host.docker.internal:5432/paperclip \
  -v "$(pwd)/data/paperclip:/paperclip" \
  ghcr.io/ben-ranford/paperclip:latest
```
