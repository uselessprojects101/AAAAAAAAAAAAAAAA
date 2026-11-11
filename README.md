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
