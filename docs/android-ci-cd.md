# Android CI/CD

This repository uses GitHub Actions to build the Expo/React Native Android app.

## Debug APK builds

The workflow `.github/workflows/android-debug-apk.yml` runs automatically when a branch push or pull request changes:

- `mobile/**`
- `android/**`
- the debug workflow itself

It installs the pinned Java/Android build environment, runs TypeScript checks and Jest tests, generates the native Android project with `expo prebuild`, builds `assembleDebug`, and uploads:

- `debug-app.apk`
- `debug-app.apk.sha256`

A manual `workflow_dispatch` run is also available.

## Release APK builds

The workflow `.github/workflows/android-release.yml` runs when a tag matching `v*` is pushed.

The tag must exactly match the Expo app version in `mobile/app.json`:

`mobile/app.json version "260608" -> tag "v260608"`

The workflow then:

1. Checks out the exact tag.
2. Validates the tag/version match.
3. Runs TypeScript and Jest checks.
4. Generates the Android project when needed.
5. Builds the debug APK.
6. Generates a SHA-256 checksum.
7. Uploads the APK as an Actions artifact.
8. Creates the GitHub Release for that tag and attaches `debug-app.apk` plus its checksum.

The release uses the GitHub Actions token, so no personal access token is required for publishing.

## Creating a release

Update the version in `mobile/app.json`, commit the change, and push a matching tag:

`v<version>`

Example:

`git tag v260608`

`git push origin v260608`

Expo SDK 55 targets Android API 36, which is why the CI environment installs Android platform 36 and build tools 36.0.0. citeturn640280search0

## Why the native Android project is generated in CI

The repository currently does not commit `mobile/android/`. Expo's prebuild step is designed to generate the native project when it does not exist, and `expo run:android` uses the same native build path. citeturn640280search6
