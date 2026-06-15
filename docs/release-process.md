# Release process

> Detailed steps are implemented as a GitHub Actions workflow in the app repo
> (`beebox-warehouse-android/.github/workflows/release.yml`). This document is
> the human-readable contract.

## Steps
1. **Bump** `versionCode`/`versionName` in the app's Gradle config; open PR; merge.
2. **Tag** `vX.Y.Z` on the app repo `main`.
3. CI **builds + signs** `app-release.apk` with the upload key (CI secret).
4. CI computes `sha256sum` of the signed APK.
5. CI **creates a GitHub Release** in `beebox-warehouse-releases` and uploads the APK asset.
6. CI **updates `version.json`** here (`latestVersionCode`, `latestVersionName`,
   `apkUrl`, `sha256`, `releaseNotes`, `publishedAt`). Bump
   `minSupportedVersionCode` only for a forced rollout.
7. Backend `/api/v1/app/version` serves/mirrors this manifest.

## Security
- The signing key never lives in source; it's a CI secret (or HSM).
- The app verifies **sha256 + APK signature** before install — a public asset
  URL is an untrusted transport endpoint.
