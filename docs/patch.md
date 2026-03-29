# Patch Catalog

This repository applies local patches to upstream `paperclipai/paperclip` before publishing the Docker image.

If you add, remove, or modify a patch in `patches/`, update this file in the same PR.

## patches/0001-dockerfile-build-and-runtime-fixes.patch

Scope:
- `Dockerfile`

Why this exists:
- Falls back from `pnpm install --frozen-lockfile` to `pnpm install --no-frozen-lockfile` so nightly publishes can continue when upstream package metadata temporarily drifts from lockfile state.
- Removes `VOLUME ["/paperclip"]` to avoid implicit anonymous volume behavior and keep host bind mount usage explicit.

Runtime effect:
- Builds are more resilient to upstream lockfile drift.
- Container storage behavior is more predictable for users mounting `/paperclip`.

## patches/0002-enable-agent-assignment.patch

Scope:
- `server/src/routes/issues.ts`

Why this exists:
- Introduces `PAPERCLIP_ALLOW_AGENT_ASSIGN=true` as an explicit bypass for agent assignment permission checks.

Runtime effect:
- When enabled, agents can assign or reassign issues without requiring `tasks:assign`.
- Default behavior is unchanged when the variable is unset or `false`.

## patches/0002-runtime-entrypoint-permission-bootstrap.patch

Scope:
- `Dockerfile`
- `docker-entrypoint.sh` (new file)

Why this exists:
- Adds a root entrypoint that prepares `/paperclip` and then drops privileges to the configured runtime UID/GID via `gosu`.
- Handles common bind mount ownership mismatches at container startup.

Runtime effect:
- Improves first-run behavior for host-mounted volumes.
- Fails early with a concrete ownership fix message when storage is not writable by the runtime user.

## patches/0003-embedded-postgres-locale-fix.patch

Scope:
- `Dockerfile`

Why this exists:
- Installs locales and sets `LANG`/`LC_ALL` to `en_US.UTF-8`, matching assumptions used by embedded PostgreSQL initialization paths.

Runtime effect:
- Prevents locale-related embedded PostgreSQL startup/init failures in containerized runs.

## patches/0004-embedded-postgres-initdb-flags-typing.patch

Scope:
- `cli/src/commands/worktree.ts`
- `packages/db/src/migration-runtime.ts`
- `server/src/index.ts`

Why this exists:
- Extends embedded PostgreSQL constructor typing to include optional `initdbFlags?: string[]`.

Runtime effect:
- Type definitions match current constructor options where `initdbFlags` may be passed.
- Reduces type mismatch and compilation friction when upstream embedded-postgres integration evolves.

## patches/0005-dockerfile-plugin-sdk-build.patch

Scope:
- `Dockerfile`

Why this exists:
- Copies plugin workspace `package.json` files into the Docker deps stage before `pnpm install`, including:
- `packages/plugins/create-paperclip-plugin`
- `packages/plugins/examples/plugin-authoring-smoke-example`
- `packages/plugins/examples/plugin-file-browser-example`
- `packages/plugins/examples/plugin-hello-world-example`
- `packages/plugins/examples/plugin-kitchen-sink-example`
- This patch was rebased to current upstream Dockerfile layout so it applies after upstream added `packages/plugins/sdk/package.json` directly.

Runtime effect:
- Keeps workspace dependency resolution stable during Docker image builds that include plugin workspaces.
- Prevents nightly publish failures caused by patch application drift against upstream Dockerfile changes.
