---
inclusion: manual
---

# Rebuild metro-core and Restart All Metro Dev Servers

This skill rebuilds metro-core and restarts all three metro dev servers (host, mini, nested-mini).

## Steps

1. Build metro-core:

```bash
npx nx build metro-core --skip-nx-cache
```

Wait for the build to complete successfully.

2. Kill existing metro processes on ports 8081, 8082, 8083:

```bash
lsof -ti:8081 | xargs kill -9 2>/dev/null
lsof -ti:8082 | xargs kill -9 2>/dev/null
lsof -ti:8083 | xargs kill -9 2>/dev/null
```

3. Start the metro dev servers SERIALLY as background processes (start one, wait for it to be ready, then start the next):

- Host (port 8081): run `NODE_OPTIONS=--conditions=dev pnpm start` in `apps/metro-example-host`
- Mini (port 8082): run `NODE_OPTIONS=--conditions=dev pnpm start` in `apps/metro-example-mini`
- Nested Mini (port 8083): run `NODE_OPTIONS=--conditions=dev pnpm start` in `apps/metro-example-nested-mini`

Use `controlBashProcess` with action `start` for each server since they are long-running processes.
Wait ~10 seconds after each start and verify the server is ready before starting the next one.

Note: Do NOT use `pnpm dev` (nodemon) — it crashes in non-interactive environments. Use `NODE_OPTIONS=--conditions=dev pnpm start` directly.
