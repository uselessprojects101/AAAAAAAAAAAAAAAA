# Flycast WASM – web player

Static site that runs the [flycast-wasm](https://github.com/nasomers/flycast-wasm) core
inside an [EmulatorJS](https://github.com/EmulatorJS/EmulatorJS) frontend. Host it on GitHub Pages.

**No BIOS or games are included.** pick their own `dc_boot.bin`, `dc_flash.bin`
and game file, nothing is uploaded anywhere

## Updating the core
Rebuild/replace `flycast_libretro.js` + `.wasm`, repack into `flycast-wasm.data`
(7z containing `flycast_libretro.js`, `flycast_libretro.wasm`, `core.json`), change `buildStart`.

## Licenses
Flycast and the core: GPLv2. EmulatorJS: GPLv3. coi-serviceworker: MIT.
