### Package Structure Division

- **`metro-core`**: Pure JS/TS, MF Runtime integration layer, zero native dependencies
- **`metro-cache`**: RN native module package, contains native code (iOS Objective-C++ + Android Kotlin) + JS business logic (CacheManager, VersionPoller, EjectionManager)
- `metro-core` dynamically loads `metro-cache` via peer dependency (optional)

## Complete Flow

### Build Side

```
Build all bundles (container + exposed + shared)
    ↓
Calculate SHA-256 for each bundle → store in bundleHashMap
    ↓
Update hash fields in manifest (metaData.buildInfo.bundleHash)
    ↓
Write final manifest → upload to Zephyr
```

### Runtime Three Paths

**1. Fallback Mode**: `__DEV__` / `metro-cache` not installed / host split bundle → use original `loadBundleAsync`

**2. Path A (Cache Hit)**:
```
getCachedBundle(bundleUrl) hits
    ↓
evaluateJavaScript(filePath) executes from disk
    ↓
Zero network requests, return immediately
```

**3. Path B (Cache Miss)**:
```
downloadFile(bundleUrl, destPath) download + write + streaming SHA-256
    ↓
Checksum verification (compare with bundleHash in manifest)
    ↓
saveBundleToCache() write MMKV or system storage with metadata
    ↓
evaluateJavaScript(destPath) execute from disk
```

### Auxiliary Capabilities

- **VersionPoller**: Background polling manifest → detect bundleUrl changes → silent download marked as pendingUpdate → activate on cold start
- **FallbackChain**: I recommend not do any fallback for error module loading, just throw error
- **EjectionManager**: Cleanup expired versions on startup + retry broken versions