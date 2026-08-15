# OEmu — Web-native emulator-style desktop

A single, self-contained `index.html` that boots into an emulator-style
desktop right inside the browser — including inside an Apple Shortcuts
**Web View** action on iPhone. No build step, no server, no external
assets. Everything runs on the device itself.

▶️ **Open `index.html`** (or view it on GitHub Pages).

## What it actually does

| Subsystem | How it runs on your device |
|---|---|
| **CPU** | Real Web Worker thread + a prime-sieve benchmark; detects cores (`navigator.hardwareConcurrency`), RAM (`navigator.deviceMemory`), WASM, SIMD, SharedArrayBuffer. |
| **GPU** | Real WebGL/WebGL2 fragment-shader raymarcher that draws on your phone's GPU; reports renderer, max texture size, viewport, and a live FPS counter. WebGPU is detected when available. |
| **Storage** | IndexedDB (`notes`, `files`, `settings`, `apps` object stores). Persists across launches entirely offline. |
| **Sensors** | `DeviceMotionEvent` (accelerometer + gyroscope), with iOS permission flow; live tilt visualization. |
| **Location** | `navigator.geolocation` with one-shot and watch mode (high accuracy). |
| **Camera** | `getUserMedia` front/back camera, snapshot to JPEG saved to IndexedDB. |
| **Haptics** | `navigator.vibrate` patterns from the toolbar, camera, sensors, and shader demo. |
| **Battery/Network** | `navigator.getBattery`, `navigator.connection`, online/offline events in the status bar. |
| **Touch UI** | Native-style app grid, draggable windows, LDPlayer-ish side toolbar, Android-style nav bar. |

## Apps included

- **Settings** — full device + GPU + sensor capability report
- **Benchmark** — CPU primes/sec (worker), GPU FPS (shader)
- **GPU Shader** — live raymarched scene on the actual GPU
- **Sensors** — accelerometer / gyroscope / geolocation
- **Camera** — live camera with snapshots persisted to IndexedDB
- **Notes** — create/edit/delete notes (IndexedDB)
- **Files** — create text files, view captured photos, export all as JSON
- **Terminal** — toy shell: `help`, `neofetch`, `info`, `ls`, `cat`, `ps`, `bench`, `geo`, `vibrate`, `clear`
- **Calculator**, **Browser** (sandboxed iframe), **About**

## Running inside Apple Shortcuts (local HTML)

1. Get `index.html` onto your iPhone (AirDrop, iCloud, Files).
2. Rename the copy to `OEmu.txt` so Shortcuts can read it.
3. Build a shortcut:
   - **Get File** → select `OEmu.txt`
   - **Base64 Encode**
   - **URL Decode** (so the HTML string is plain)
   - **Show Web Page** (or **Show in Web View**) with the decoded text
4. Run it. The page boots inside the in-app web view with no network.

> **Camera / motion note:** iOS only grants camera and DeviceMotion
> permissions to pages served over HTTPS or from a secure origin. The
> Shortcuts "Show Web Page" action uses an `about:srcdoc` / internal
> origin, so camera and motion may be denied by iOS itself; everything
> else (GPU, CPU, IndexedDB, touch, geolocation if allowed) works.
> For the full experience, host the file on GitHub Pages (HTTPS) and
> open it in Safari, then "Add to Home Screen."

## Deploy to GitHub Pages

```bash
git commit -am "OEmu web runtime"
git push origin arena/01a0069e-oemu
```

Then in the repo: **Settings → Pages → Branch: `main` (or your branch) / folder `/` → Save.**
`.nojekyll` is included so Pages serves the HTML as-is.

## What this is *not*

This is **not** a port of LDPlayer and cannot run Android `.apk` files or
native ARM/x86 binaries. The original `LDPlayer9_ens_*.exe` is a 3.5 MB
online *installer* that downloads a full VirtualBox-based x86 Android VM
with kernel drivers — none of which can run inside a browser sandbox,
especially on iOS. OEmu instead uses the host device's CPU/GPU/sensors
directly via web APIs.
