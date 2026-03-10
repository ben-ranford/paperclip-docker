# Paperclip Docker Mirror

[![Nightly Publish](https://img.shields.io/github/actions/workflow/status/ben-ranford/paperclip-docker/upstream-sync-and-release.yml?label=nightly%20publish)](https://github.com/ben-ranford/paperclip-docker/actions/workflows/upstream-sync-and-release.yml)
[![GHCR](https://img.shields.io/badge/GHCR-ghcr.io%2Fben--ranford%2Fpaperclip-2ea44f?logo=github)](https://ghcr.io/ben-ranford/paperclip)

This repository publishes a Docker image for Paperclip from upstream `paperclipai/paperclip`.

Upstream source of truth:
- [paperclipai/paperclip](https://github.com/paperclipai/paperclip)

Latest image:
- [ghcr.io/ben-ranford/paperclip:latest](https://ghcr.io/ben-ranford/paperclip)

## What It Does

- Runs nightly with GitHub Actions.
- Checks the latest upstream `master` commit SHA.
- Skips builds when the upstream SHA was already published.
- Clones upstream source at that SHA.
- Applies local patch files from `patches/*.patch`.
- Builds and pushes multi-arch images to GHCR when a new upstream SHA appears.

Patch files applied during build:

- `patches/0001-dockerfile-build-and-runtime-fixes.patch`

## Using The Docker Image

Pull:

```sh
docker pull ghcr.io/ben-ranford/paperclip:latest
```

Prepare a writable data directory for the container user (`uid=1000`):

```sh
mkdir -p ./data/paperclip
sudo chown -R 1000:1000 ./data/paperclip
```

### Environment Variables

Required:

- `BETTER_AUTH_SECRET`: auth/session signing secret for authenticated mode.

Common:

- `HOST` (default: `0.0.0.0`)
- `PORT` (default: `3100`)
- `PAPERCLIP_HOME` (default in image: `/paperclip`)
- `PAPERCLIP_PUBLIC_URL` (recommended for callback/link correctness)
- `DATABASE_URL` (optional; if unset, Paperclip uses embedded PostgreSQL)
- `OPENAI_API_KEY` (optional; for Codex adapter)
- `ANTHROPIC_API_KEY` (optional; for Claude adapter)

Security and deployment:

- `PAPERCLIP_DEPLOYMENT_MODE` (default: `authenticated`)
  - `authenticated`: authentication enabled.
  - `local_trusted`: non-authenticated local trusted mode.
- `PAPERCLIP_DEPLOYMENT_EXPOSURE` (default: `private`)
- `PAPERCLIP_AUTH_PUBLIC_BASE_URL` (optional explicit auth base URL)
- `PAPERCLIP_ALLOWED_HOSTNAMES` (optional comma-separated allowlist)
- `PAPERCLIP_ALLOW_AGENT_ASSIGN` (optional; default: disabled)
  - `true`: allow agents to assign/reassign issues without `tasks:assign`.
  - `false`/unset: keep normal assignment permission checks.

Storage and backups (optional advanced):

- `PAPERCLIP_STORAGE_PROVIDER` (`local_disk` or `s3`)
- `PAPERCLIP_STORAGE_S3_BUCKET`, `PAPERCLIP_STORAGE_S3_REGION`, `PAPERCLIP_STORAGE_S3_ENDPOINT`
- `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `AWS_SESSION_TOKEN`
- `PAPERCLIP_DB_BACKUP_ENABLED`, `PAPERCLIP_DB_BACKUP_INTERVAL_MINUTES`, `PAPERCLIP_DB_BACKUP_RETENTION_DAYS`

### Example: Embedded PostgreSQL

```sh
docker run --rm \
  -p 3100:3100 \
  -e BETTER_AUTH_SECRET=replace-with-strong-secret \
  -e PAPERCLIP_PUBLIC_URL=http://localhost:3100 \
  -v "$(pwd)/data/paperclip:/paperclip" \
  ghcr.io/ben-ranford/paperclip:latest
```

### Example: External PostgreSQL

```sh
docker run --rm \
  -p 3100:3100 \
  -e BETTER_AUTH_SECRET=replace-with-strong-secret \
  -e PAPERCLIP_PUBLIC_URL=http://localhost:3100 \
  -e DATABASE_URL=postgres://paperclip:paperclip@host.docker.internal:5432/paperclip \
  -v "$(pwd)/data/paperclip:/paperclip" \
  ghcr.io/ben-ranford/paperclip:latest
```
