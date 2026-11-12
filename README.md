# Flycast WASM – web player

Static site that runs the [flycast-wasm](https://github.com/nasomers/flycast-wasm) core
inside an [EmulatorJS](https://github.com/EmulatorJS/EmulatorJS) frontend. Host it on GitHub Pages.

**No BIOS or games are included.** Visitors pick their own `dc_boot.bin`, `dc_flash.bin`
and game file; nothing is uploaded anywhere (BIOS is cached in the visitor's own browser).

## Deploy to GitHub Pages
1. Create a repo (e.g. `dreamcast`) and push the contents of this folder to the root of `main`.
2. Repo **Settings → Pages → Build and deployment → Deploy from a branch → `main` / `(root)`**.
3. Open `https://<you>.github.io/<repo>/`. The first visit reloads once (see below).

## Layout
- `index.html` – launcher UI (BIOS + game pickers, core options)
- `coi-serviceworker.js` – adds the COOP/COEP headers the core wants (Pages can't set headers)
- `data/` – EmulatorJS frontend (`consts.js` patched: `dreamcast -> flycast`, WebGL2 required)
- `data/cores/flycast-wasm.data` – the core, packed as 7z (js + wasm + core.json)
- `data/cores/reports/flycast.json` – cache-busting stamp; bump `buildStart` when you replace the core
- `config-reference/` – the original core.json / option files from flycast-wasm

## Build number
Bump `window.BUILD` in `index.html` on every redeploy; it shows under the Start button and in the Log panel.

## Updating the core
Rebuild/replace `flycast_libretro.js` + `.wasm`, repack into `flycast-wasm.data`
(7z containing `flycast_libretro.js`, `flycast_libretro.wasm`, `core.json`), change `buildStart`.

## Licenses
Flycast and the core: GPLv2. EmulatorJS: GPLv3. coi-serviceworker: MIT.
Keep the source available (link to the flycast-wasm repo) if you publish this.

## Known core issue: .cue discs
The core is built without an explicit `STACK_SIZE`, so it likely gets Emscripten's 64 KB default,
and Flycast's CUE parser allocates a 64 KB buffer on the stack. `.cue` games probably crash at load;
use CHD, GDI or CDI instead. Upstream fix: add `-s STACK_SIZE=1MB` to `build-prod.sh` and rebuild.

## Performance notes
Measured on a mid-range Windows PC in Firefox 160: ~26 ms/frame (≈35-38 FPS) on the Dreamcast BIOS menu.
Timer resolution was fine (0.02 ms), so pacing isn't a timer problem; emulation speed is the limit.
The AICA (ARM7 + DSP) is upstream code without a JIT, so the launcher has a "Speed mode" that disables the DSP
and enables auto frame-skip. Compare browsers with the frame-timing line in the Log panel (60 FPS = 16.7 ms).

## Threads setting
EmulatorJS remembers a "Threads: enabled" toggle per browser and would then request a non-existent
`flycast-thread-wasm.data`. This site ships only the single-threaded core, so the toggle is removed
and the threaded variant is never requested.

Edge 157 on the same PC (i5-9500, RTX 3050), BIOS menu with Speed mode on (320x240 internal resolution chosen by the tester;
640x480 was also fine): ~15 ms/frame (≈66 FPS), p95 23 ms. Firefox was slow with Speed mode on as well, so the browser is the cause.

## Performance profiles and the HUD
- Launcher "Performance profile": **Balanced** (core defaults), **Speed** (audio DSP off, auto frame-skip),
  **Maximum** (also 320x240, no fog, anisotropic off, maximum frame-skip). ChromeOS defaults to Maximum, other systems to Speed.
- "Show performance HUD" turns on the core's built-in HUD (`?hud=1`). It shows FPS actual/target, frame time min/avg/max (`ft`),
  emulation time (`core`), render parse/sort/draw time (`rp`), and audio-chip time (`aica`) in ms per frame, plus audio queue
  stats (`wq`, `un` underruns, `dr` drops). The Log panel keeps the last HUD samples, so Copy includes them.
- Reading it: if `core` is the biggest number the SH4 JIT is the limit (only frame-skip/underclock help); if `rp` draw is big the GPU is
  the limit (resolution/fog help); if `aica` is big the audio chip is the limit (DSP is already off in Speed/Maximum).
