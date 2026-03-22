# ThinkCorney migration notes

This repo has been normalized for current ZMK / Zephyr 4.1-era builds:

- `build.yaml` now targets `nice_nano@2.0.0`
- the shared GitHub Actions workflow is used
- custom shield files live in `boards/shields/think_corney/`
- a mirrored `config/boards/shields/think_corney/` path is kept for compatibility with user-config tooling/search paths
- HWMv2-style board overlay names were added (`nice_nano_nrf52840_zmk.overlay`)
- `kscan` now has `wakeup-source;` so deep sleep can wake reliably
- the former `config_base` submodule contents are vendored directly so builds do not depend on recursive submodule checkout
- TrackPoint support remains module-based through `kb_zmk_ps2_mouse_trackpoint_driver`

## Important note about TrackPoint support

This repo still defaults to upstream `zmk/main` in `config/west.yml` to stay on the latest ZMK/Zephyr line.
If the external PS/2 TrackPoint module regresses against upstream pointer APIs again,
there is a commented fallback branch in `config/west.yml`.
