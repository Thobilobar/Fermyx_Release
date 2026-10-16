[README.md](https://github.com/user-attachments/files/33520246/README.md)
# Fermyx 1.0.0

See the [diagnostics guide](docs/DIAGNOSTICS_GUIDE.md) for log locations, event fields, and the Windows collection command.

Fermyx is a Windows desktop audio mixer and toolkit. The Tauri shell and
dedicated Rust Core manage Windows audio sessions and devices. The Vite frontend
provides the Mixer, Devices & Routing, Sound, Audio Tools, Soundboard, Recording,
Profiles, Rules, Hotkeys, and Activity pages. A separate .NET Sound Host and
native DSP component support live processing and recording.

## Develop

Use a supported Node.js release (`^22.22.2`, `^24.15.0`, or `>=26`), Rust with
the MSVC target, the .NET 9 SDK, MSVC C++ Build Tools, the Windows SDK, and
WebView2. In a Windows PowerShell terminal:

```powershell
npm ci
npm run tauri:dev
```

The Sound Host and native DSP compile before Tauri starts. `?dev=1` is an
explicit frontend preview mode; it does not simulate real Windows audio in a
release build.

## Verify and build

```powershell
npm run release:check
```

This runs the full project audit and Vite production build. For a signed
Windows NSIS release, follow [the Windows build guide](docs/BUILD_WINDOWS.md)
and [release checklist](docs/RELEASE_CHECKLIST.md). A normal `tauri:build` is
useful for local tests but does not create the signed notification identity.

The public version is **1.0.0**. `release/identity.json` contains a separate,
monotonically increasing Windows package version for notification registration;
this allows upgrades from the prior 24.x development identity. Run
`npm run release:sync` after intentionally changing the app version, then
`npm run release:check`. Preserve the Tauri identifier and persisted state
schema unless a separately tested data migration accompanies the change.

## Source layout

| Path | Purpose |
| --- | --- |
| `src/`, `index.html` | Frontend, native bridge, styles and locales |
| `src-tauri/src/` | Shell, Core process, Windows audio and data migration |
| `src-tauri/sound_host/`, `route_policy/`, `native_audio/` | Audio helper processes and DSP |
| `src-tauri/windows/identity/` | Windows sparse identity manifest and installer hooks |
| `scripts/`, `native-tests/` | Build tooling and regression checks |
| `docs/` | Current release guidance and historical engineering reports |

User data is stored in `%APPDATA%\Fermyx`; local caches and WebView2 data are
in `%LOCALAPPDATA%\Fermyx`. The app migrates older
`com.fermyx.desktop` folders without overwriting newer data. The 1.0.0 label
does not reset profiles, rules, recordings, or settings. See
[Windows identity and data](docs/windows-identity.md).

Live Sound requires VB-CABLE on the user's PC. Fermyx offers the in-app setup
flow; audio and device behavior still needs testing on actual Windows hardware.
Historical development reports are kept in [`docs/history/`](docs/history/).
