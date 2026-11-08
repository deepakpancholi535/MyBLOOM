# MyBLOOM build and safety guide

## Build

From the repository root:

```powershell
bun.cmd install --frozen-lockfile
bun.cmd run build
Set-Location src-tauri
cargo check --locked
cargo clippy --locked --all-targets
cargo test --locked
Set-Location ..
bun.cmd run tauri build
```

The frontend typecheck and Vite build pass in this workspace. Rust commands are
currently blocked on this machine by Windows Application Control policy
(`os error 4551`) before Cargo build scripts can execute, so no installer has
been produced here yet. A successful Tauri build will place artifacts under
`src-tauri\target\release\bundle\`.

## Taskbar recovery

The application retains its crash marker, watchdog, and taskbar restoration
paths. If the taskbar stays hidden, press `Ctrl+Shift+Esc`, choose **Run new
task**, enter `explorer.exe`, and press Enter. Exit through the tray menu when
possible; it restores the native taskbar.

## Network behavior

With weather disabled, the application has no configured external network host.
When enabled, city search contacts `geocoding-api.open-meteo.com` and forecast
requests contact `api.open-meteo.com`, using only the city or coordinates chosen
by the user. Remote announcements, IP geolocation, auto-updates, and Google
Fonts were removed. The CSP allows only these weather hosts plus local IPC.

The static source review found only Open-Meteo API URLs, local development IPC,
Windows WLAN XML namespaces, SVG namespaces, and a unit-test
`https://example.com` value. No updater or remote announcement endpoint remains.

## Audit limits

The Rust source was searched for network clients, process launching, registry
access, unsafe code, and IPC commands. Bluetooth RFCOMM and WLAN APIs are local
device/network-management operations, not remote telemetry. The app's
diagnostics and previews remain local. This is a source review and cannot prove
the absence of behavior in every third-party dependency; runtime verification
should still be performed with Resource Monitor or `Get-NetTCPConnection`.
