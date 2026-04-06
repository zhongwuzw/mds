---
inclusion: manual
---

# Rebuild Metro-Core, Restart Servers, and Compute Bundle Hashes

This skill combines two skills in sequence: first rebuilds metro-core and restarts all metro dev servers, then computes bundle hashes and updates manifests.

## Steps

1. Execute the `rebuild-and-restart-metro` skill (build metro-core, kill existing metro processes, start all three dev servers).

2. Wait for all three metro dev servers to be fully ready and serving bundles (ports 8081, 8082, 8083). Verify by checking process output for signs that metro is ready (e.g. "Welcome to Metro" or similar startup messages). Retry checks a few times with short delays if needed.

3. Once all servers are confirmed ready, execute the `compute-bundle-hashes` skill (download bundles, compute SHA-256 hashes, update mf-manifest.json files, clean up).
