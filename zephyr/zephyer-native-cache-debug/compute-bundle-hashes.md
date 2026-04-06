---
inclusion: manual
---

# Compute Bundle Hashes and Update Manifests

This skill downloads dev bundles from local metro servers, computes their SHA-256 hashes, and injects the hashes into the corresponding `mf-manifest.json` files.

## Steps

1. Download the following four bundles to a temp directory:

```bash
curl -s -o /tmp/nestedMini.bundle "http://localhost:8083/nestedMini.bundle?platform=ios&dev=true&lazy=true&minify=false&runModule=true&modulesOnly=false&excludeSource=true&sourcePaths=url-server"
curl -s -o /tmp/mini.bundle "http://localhost:8082/mini.bundle?platform=ios&dev=true&lazy=true&minify=false&runModule=true&modulesOnly=false&excludeSource=true&sourcePaths=url-server"
curl -s -o /tmp/nested-mini-info.bundle "http://localhost:8083/src/nested-mini-info.bundle?platform=ios&dev=true&lazy=true&minify=false&runModule=false&modulesOnly=true&excludeSource=true&sourcePaths=url-server"
curl -s -o /tmp/info.bundle "http://localhost:8082/src/info.bundle?platform=ios&dev=true&lazy=true&minify=false&runModule=false&modulesOnly=true&excludeSource=true&sourcePaths=url-server"
```

2. Compute SHA-256 hash for each bundle:

```bash
shasum -a 256 /tmp/nestedMini.bundle
shasum -a 256 /tmp/mini.bundle
shasum -a 256 /tmp/nested-mini-info.bundle
shasum -a 256 /tmp/info.bundle
```

3. Update `apps/metro-example-mini/node_modules/.mf-metro/mf-manifest.json`:
   - Set `metaData.buildInfo.hash` to the hash of `mini.bundle`
   - Find the expose entry with `name: "info"` and set its `hash` to the hash of `info.bundle`

4. Update `apps/metro-example-nested-mini/node_modules/.mf-metro/mf-manifest.json`:
   - Set `metaData.buildInfo.hash` to the hash of `nestedMini.bundle`
   - Find the expose entry with `name: "nested-mini-info"` (or whichever expose exists) and set its `hash` to the hash of `nested-mini-info.bundle`

5. Clean up temp files:

```bash
rm /tmp/nestedMini.bundle /tmp/mini.bundle /tmp/nested-mini-info.bundle /tmp/info.bundle
```

## Hash Placement Reference

In `mf-manifest.json`, hashes go in these locations:

- Container bundle hash → `metaData.buildInfo.hash`
- Exposed module hash → `exposes[].hash` (matched by expose name)
- Shared module hash → `shared[].hash` (if applicable)
