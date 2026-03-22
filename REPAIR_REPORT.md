# ThinkCorney ZMK repair report

This snapshot contains the project after structural and logical fixes aimed at restoring builds on the current ZMK user-config/module layout while preserving:
- split build
- nice!view display support
- PS/2 TrackPoint support
- existing keymap/base config behavior

## What was fixed

1. Added `zephyr/module.yml`
   - Required so current `zmkfirmware/zmk/.github/workflows/build-user-config.yml` treats this repo as an extra ZMK module and loads `boards/shields/think_corney`.

2. Kept `config_base` as real files in this snapshot
   - In the original repo, `config_base` was an orphaned gitlink/submodule entry without a `.gitmodules` mapping.
   - GitHub Actions checkout would not reliably provide its contents, which breaks includes such as `config_base/config/base.keymap`.
   - This snapshot vendors those files directly so the project is self-contained.

3. Moved right-half-only TrackPoint devicetree config out of shared keymap includes
   - Shared keymap include files referenced labels like `&mouse_ps2` that only exist on the right/central half.
   - That can break the left-half build during devicetree compilation.
   - TrackPoint hardware tuning and enablement now live in `boards/shields/think_corney/think_corney_right.overlay`.

4. Fixed display/TrackPoint pin conflict
   - `nice_view` CS stayed on `pro_micro 9`.
   - TrackPoint reset moved from `pro_micro 9` to `pro_micro 10`.

## Files changed

- `zephyr/module.yml`
- `boards/shields/think_corney/think_corney_right.overlay`
- `config_base/config/base.keymap`
- `config_base/config/includes/mouse_keys.dtsi`
- `config/includes/mouse_tp.dtsi`

## If you want to apply this back into your Git repo

Because the original repo tracks `config_base` as a gitlink, replace the repo contents with this snapshot or convert `config_base` into normal tracked files before committing.

Typical sequence:

```bash
git rm --cached config_base
rm -rf .git/modules/config_base config_base/.git
cp -R /path/to/this/fixed/config_base ./config_base
git add config_base zephyr/module.yml \
  boards/shields/think_corney/think_corney_right.overlay \
  config_base/config/base.keymap \
  config_base/config/includes/mouse_keys.dtsi \
  config/includes/mouse_tp.dtsi
git commit -m "Fix ThinkCorney ZMK module structure and TrackPoint/display config"
```
