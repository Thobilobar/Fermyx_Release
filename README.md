[README.md](https://github.com/user-attachments/files/33541922/README.md)
# Fermyx

Fermyx is a Windows desktop audio workspace for per-app mixing and routing, profiles, rules, live sound processing, audio tools, recording, and a soundboard. The source is proprietary and belongs in a **private** repository. This README describes the codebase; it is not a public source license or a release announcement.

## Architecture

| Component | Responsibility |
| --- | --- |
| `src/`, `index.html` | Tauri WebView UI, localization, state, and native bridge |
| `src-tauri/src/` | Desktop shell, dedicated Rust Core, Windows audio and persistence |
| `src-tauri/sound_host/`, `native_audio/`, `route_policy/` | Live processing, DSP and Windows routing helpers |
| `src-tauri/windows/identity/` | Windows app identity and installer hooks |
| `scripts/`, `native-tests/` | Release preparation and regression checks |
| `docs/` | Engineering, diagnostics, privacy, and Windows build guidance |

The UI sends versioned requests to a dedicated Core process. Native audio and long-running work stay off the WebView thread. Signed releases use the Tauri updater and a public GitHub **release repository** as the binary distribution endpoint. Keep the source repository private; publish only intended installer, signature, and manifest assets.

## Windows development

Install a supported Node.js version (`^22.22.2`, `^24.15.0`, or `>=26`), Rust with the MSVC target, .NET 9 SDK, MSVC C++ Build Tools, Windows SDK, and WebView2. In PowerShell at the project root:

```powershell
npm ci
npm run tauri:dev
```

The dev task compiles the Sound Host and native DSP before launching the shell. Frontend preview mode does not reproduce Windows device routing. Live Sound requires VB-CABLE; Fermyx provides an in-app setup flow. Test audio changes on real Windows hardware.

## Verification and release

```powershell
npm run release:check
```

See [Windows build](docs/BUILD_WINDOWS.md) and [release checklist](docs/RELEASE_CHECKLIST.md). For each release, update `package.json`, run `npm run release:sync`, then `npm run release:check`. Sign the updater bundle with a private key stored outside the repository. The public key and update endpoint are build configuration; never commit the private key, password, personal diagnostics, or user data. The NSIS install location follows the installed executable; user data is separate from the install directory.

## Data and diagnostics

Settings, profiles, rules, recordings, and activity history are kept in `%APPDATA%\Fermyx`; cache and WebView2 data live under `%LOCALAPPDATA%\Fermyx`. Updates must preserve these directories. See [Windows identity and data](docs/windows-identity.md), [diagnostics](docs/DIAGNOSTICS_GUIDE.md), and the bundled privacy policy. Diagnostics can contain machine paths, process names, and audio device details: remove sensitive fields before sharing.

## Ownership and third-party notices

All Fermyx-specific source is private unless separately licensed. Third-party components keep their own licenses and notices. Consult the bundled third-party notices and legal release audit before shipping binaries. This README does not grant redistribution rights to the source tree.
