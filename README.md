# frontpage-data

Build artifacts and metadata for [Frontpage](https://github.com/hartlco/frontpage) — a self-hosted app distribution server.

This repo is the data store. The Frontpage server clones it and serves builds from it. You don't edit this repo by hand — use the `frontpage-cli` tool to publish builds.

## Folder Structure

```
apps/
  {app-slug}/
    app.json                    # App metadata
    icon.png                    # App icon (optional, 512x512 recommended)
    appcast.xml                 # Sparkle feed (macOS apps only, auto-generated)
    builds/
      {version}-{build_number}/
        build.json              # Build metadata
        Artifact.ipa|.apk|.dmg  # The build artifact
```

## JSON Schemas

### app.json

```json
{
  "name": "My App",
  "slug": "my-app",
  "platform": "ios",
  "bundle_id": "co.hartl.myapp",
  "description": "A short description of the app",
  "icon": "icon.png"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Display name |
| `slug` | string | URL-safe identifier (must match folder name) |
| `platform` | string | `ios`, `android`, or `macos` |
| `bundle_id` | string | Bundle identifier |
| `description` | string | Short description shown in the UI |
| `icon` | string | Filename of the icon image (relative to app folder) |

### build.json

```json
{
  "version": "1.2.0",
  "build_number": "87",
  "date": "2026-04-03T14:30:00Z",
  "notes": "Bug fixes and performance improvements",
  "file": "MyApp.ipa",
  "size": 25000000,
  "min_os_version": "16.0",
  "sparkle_signature": ""
}
```

| Field | Type | Description |
|-------|------|-------------|
| `version` | string | Semantic version |
| `build_number` | string | Build number |
| `date` | string | ISO 8601 timestamp |
| `notes` | string | Release notes |
| `file` | string | Artifact filename (must match the actual file) |
| `size` | number | File size in bytes |
| `min_os_version` | string | Minimum OS version (optional) |
| `sparkle_signature` | string | EdDSA signature for Sparkle (macOS, optional) |

## Git LFS

Binary artifacts are tracked with Git LFS. The `.gitattributes` file configures this automatically.

Tracked extensions: `*.ipa`, `*.apk`, `*.dmg`, `*.zip`

## Publishing Builds

Use the CLI tool from the [frontpage](https://github.com/hartlco/frontpage) repo:

```bash
go install github.com/hartlco/frontpage/cmd/frontpage-cli@latest

frontpage-cli publish \
  --data-dir . \
  --platform ios \
  --app my-app \
  --version 1.2.0 \
  --build 87 \
  --file /path/to/MyApp.ipa \
  --notes "Bug fixes" \
  --base-url https://builds.hartl.co
```

The CLI creates the folder structure, writes the JSON files, generates `appcast.xml` for macOS apps, and commits + pushes.
