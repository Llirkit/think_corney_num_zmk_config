# ThinkCorney migration notes

This repo was reshaped from the old pre-module layout to the current ZMK user-config layout:

- shield definitions moved from `config/boards/shields/...` to `boards/shields/...`
- GitHub Actions switched to `zmkfirmware/zmk/.github/workflows/build-user-config.yml@main`
- `config/west.yml` now uses external modules instead of the old baked-in PS/2 fork
- the TrackPoint setup was rewritten to use `kb_zmk_ps2_mouse_trackpoint_driver`
- pointing feature flag updated to `CONFIG_ZMK_POINTING=y`

## Important note about TrackPoint support

The PS/2 TrackPoint module historically lagged behind upstream ZMK pointer changes.
This repo therefore defaults to upstream `zmk/main`, but keeps a commented fallback in
`config/west.yml` (`infused-kim/pr-testing/mouse_ps2_module_base`) in case the current
module still needs the older pointer branch.
