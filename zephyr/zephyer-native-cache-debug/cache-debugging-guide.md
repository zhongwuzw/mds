---
inclusion: manual
---

# Native Cache Debugging Guide

This guide demonstrates how to debug the zephyr-native-cache feature in dev mode using metro-example-host / metro-example-mini / metro-example-nested-mini.

## Architecture Overview

```
┌─────────────────────────────────┐
│  Host (port 8081)               │
│  - Loads Mini and NestedMini    │
│  - Registers zephyr-native-cache│
│  - forceCacheInDev: true        │
└────────┬──────────┬─────────────┘
         │          │
         ▼          ▼
┌──────────────┐  ┌───────────────────────┐
│ Mini (8082)  │  │ NestedMini (8083)     │
│ exposes:     │  │ exposes:              │
│   ./info     │  │   ./nestedMiniInfo    │
│              │◄─│ remotes: mini         │
└──────────────┘  └───────────────────────┘
```

- **Host** is the only app that needs cache configuration
- **Remote apps** (Mini / NestedMini) require no cache setup

## Prerequisites

1. pnpm and Node.js >= 20 installed
2. `pnpm install` has been run in the `core/` directory
3. The `zephyr-packages` repo has been cloned alongside `core/`, checked out to the branch from PR [#412](https://github.com/ZephyrCloudIO/zephyr-packages/pull/412) (this branch contains the `zephyr-native-cache` caching capability)

Expected directory structure:

```
zephyr/
├── core/                  # @module-federation/metro etc.
└── zephyr-packages/       # zephyr-native-cache source (PR #412 branch)
    └── libs/
        └── zephyr-native-cache/
```

## Step 1: Integrate Cache in Host (Already Done)

The Host app requires 3 configuration changes:

### 1.1 Add Dependency

In `apps/metro-example-host/package.json`, add a link dependency:

```json
{
  "devDependencies": {
    "zephyr-native-cache": "link:../../../zephyr-packages/libs/zephyr-native-cache"
  }
}
```

### 1.2 Configure Metro Resolution

In `apps/metro-example-host/metro.config.js`:

```js
const config = {
  resolver: {
    extraNodeModules: {
      // Ensure Metro can resolve zephyr-native-cache
      'zephyr-native-cache': path.resolve(
        __dirname,
        'node_modules/zephyr-native-cache',
      ),
    },
  },
  // Watch zephyr-native-cache source for live reload during development
  watchFolders: [
    path.resolve(__dirname, '../../../zephyr-packages/libs/zephyr-native-cache'),
  ],
};
```

### 1.3 Register Cache

In `apps/metro-example-host/index.js`, call `register` **before** `AppRegistry.registerComponent`:

```js
import { register } from 'zephyr-native-cache';

// forceCacheInDev: true enables cache in dev mode for debugging
register({ forceCacheInDev: true });
```

## Step 2: Build metro-core and Start Servers

After modifying metro-core source, you need to rebuild and restart all Metro servers.

### Option A: Use Skill Automation

Run the [rebuild-and-restart-metro](./rebuild-and-restart-metro.md) skill to automate all steps below.

### Option B: Manual Steps

```bash
# 1. Build metro-core in the core/ directory
cd /path/to/zephyr/core
npx nx build metro-core --skip-nx-cache

# 2. Kill existing Metro processes
lsof -ti:8081 | xargs kill -9 2>/dev/null
lsof -ti:8082 | xargs kill -9 2>/dev/null
lsof -ti:8083 | xargs kill -9 2>/dev/null

# 3. Start three Metro servers sequentially (wait for each to be ready)
# Host (8081)
cd apps/metro-example-host
pnpm start

# Mini (8082)
cd ../metro-example-mini
pnpm start

# Nested Mini (8083)
cd ../metro-example-nested-mini
pnpm start 
```

## Step 3: Compute Bundle Hashes and Inject into Manifests

The cache relies on SHA-256 hashes in `mf-manifest.json` for each bundle. The cache layer compares hashes to determine whether a bundle needs updating.

### Option A: Use Skill Automation

Run the [compute-bundle-hashes](./compute-bundle-hashes.md) skill (requires all three Metro servers to be ready).

Or run the [rebuild-and-compute-hashes](./rebuild-and-compute-hashes.md) skill (build + start + compute hashes in one step).

### Option B: Manual Steps

```bash
# 1. Download dev bundles to a temp directory
curl -s -o /tmp/mini.bundle \
  "http://localhost:8082/mini.bundle?platform=ios&dev=true&lazy=true&minify=false&runModule=true&modulesOnly=false&excludeSource=true&sourcePaths=url-server"
curl -s -o /tmp/info.bundle \
  "http://localhost:8082/src/info.bundle?platform=ios&dev=true&lazy=true&minify=false&runModule=false&modulesOnly=true&excludeSource=true&sourcePaths=url-server"
curl -s -o /tmp/nestedMini.bundle \
  "http://localhost:8083/nestedMini.bundle?platform=ios&dev=true&lazy=true&minify=false&runModule=true&modulesOnly=false&excludeSource=true&sourcePaths=url-server"
curl -s -o /tmp/nested-mini-info.bundle \
  "http://localhost:8083/src/nested-mini-info.bundle?platform=ios&dev=true&lazy=true&minify=false&runModule=false&modulesOnly=true&excludeSource=true&sourcePaths=url-server"

# 2. Compute SHA-256 hashes
shasum -a 256 /tmp/mini.bundle
shasum -a 256 /tmp/info.bundle
shasum -a 256 /tmp/nestedMini.bundle
shasum -a 256 /tmp/nested-mini-info.bundle

# 3. Inject hashes into the corresponding mf-manifest.json files
#    Mini:        apps/metro-example-mini/node_modules/.mf-metro/mf-manifest.json
#    NestedMini:  apps/metro-example-nested-mini/node_modules/.mf-metro/mf-manifest.json
```

### Hash Injection Locations

In `mf-manifest.json`:

| Bundle Type | JSON Path | Example |
|---|---|---|
| Container bundle | `metaData.buildInfo.hash` | hash of mini.bundle |
| Exposed module | `exposes[name="info"].hash` | hash of info.bundle |
| Shared module | `shared[name="lodash"].hash` | hash of lodash.bundle |

## Step 4: Verify on iOS Simulator

```bash
# Launch the iOS app from the Host directory
cd apps/metro-example-host
pnpm ios
```
