## Core Architecture Decisions

Our solution is based on the following key design decisions:

### 1. Cache Bundles Only, Not Manifests

- **Manifest is a runtime contract** (shared negotiation, expose mapping). Zephyr tag URLs are dynamic pointers - caching would lead to stale metadata
- **Bundle URLs are immutable** (each version has a unique URL containing username, buildId, content hash), naturally suitable as cache keys
- **Manifest always fetched from network**, handled by existing `SnapshotHandler`, flow remains unchanged

### 2. Native Layer Closed Loop

- In production mode, `@expo/metro-runtime`'s `buildUrlForBundle` only accepts `http://`/`https://` protocols - `file://` throws exceptions
- Download, storage, verification, and execution all sink to RN native module (`NativeMFECache`)
- `downloadFile(url, destPath)` → native layer uses `NSURLSession`/`OkHttp` to download while streaming SHA-256 calculation
- `evaluateJavaScript(filePath)` → native layer reads disk file and executes directly via JSI `runtime.evaluateJavaScript()`

### 3. Cache Layer Insertion Point: `asyncRequire.ts`'s `buildLoadBundleAsyncWrapper`

- Remote build artifacts include container bundle + exposed module bundle, both need caching
- Both go through `asyncRequire.ts`'s `buildLoadBundleAsyncWrapper` → inner `loadBundleAsync`
- `loadEntry` hook only intercepts container bundle, cannot cover exposed module bundles
- Cache layer placed inside wrapper, uses `isUrl(bundlePath)` to distinguish remote bundles (full URL) from host split bundles (relative path)


### 4.Package Structure Division

- **`metro-core`**: Pure JS/TS, MF Runtime integration layer, zero native dependencies
- **`metro-cache`**: RN native module package, contains native code (iOS Objective-C++ + Android Kotlin) + JS business logic (CacheManager, VersionPoller, EjectionManager)
- `metro-core` dynamically loads `metro-cache` via peer dependency (optional)

### 5. JS Layer Business Logic in `metro-cache` Package

- CacheManager, VersionPoller, FallbackChain, EjectionManager and other business logic all in JS layer (`metro-cache` package)
- Reasons: Need to access MF Runtime state, integrate with React lifecycle, business logic flexibility
- Native layer only provides low-level capabilities: file system, HTTP download, SHA-256, JS execution

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
- **FallbackChain**: No fallback for error module loading - just throw error and let business logic perceive it. Reason: we cannot guarantee that fallback versions won't cause business issues. The recommended flow: 1) throw error to surface the problem, 2) business team fixes the issue, 3) rely on background polling to download new bundle, 4) next load will succeed with the fixed version
- **EjectionManager**: Cleanup expired versions on startup + retry broken versions