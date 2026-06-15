# beebox-warehouse-releases

Public distribution channel for the **BeeBox Warehouse** Android handheld app.
Holds signed release APKs (as GitHub Release assets) and the update manifest the
in-app updater reads.

This repo contains **no source** — the app lives in the private
`beebox-warehouse-android` repo. Releases are published here by CI.

## Update manifest

`version.json` (served/mirrored by the backend at `GET /api/v1/app/version`) is
the contract the in-app updater consumes:

| field | meaning |
|---|---|
| `latestVersionCode` | integer; app compares against its installed `versionCode` |
| `latestVersionName` | human-readable version |
| `minSupportedVersionCode` | below this → **forced** update |
| `apkUrl` | HTTPS URL of the signed APK (a GitHub Release asset here) |
| `sha256` | hex digest the app verifies **before** install |
| `releaseNotes` | shown in the update prompt |
| `publishedAt` | ISO-8601 publish time |

The app downloads `apkUrl`, verifies `sha256` **and** the APK signature, then
installs via `PackageInstaller`. The gateway stays the source of truth for
`minSupportedVersionCode` so rollouts/force-updates are controlled server-side.

## Release process (CI, to be wired in `beebox-warehouse-android`)

1. Tag a release in the app repo → CI builds + **signs** the APK.
2. CI creates a GitHub Release **here** and uploads the signed APK asset.
3. CI computes `sha256` and updates `version.json` in this repo.
4. Backend `/api/v1/app/version` reflects the new manifest.

Signing-key custody + the CI secret that signs/uploads are an ops to-do.
