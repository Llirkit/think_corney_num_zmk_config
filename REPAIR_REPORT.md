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

## Additional fix after GitHub Actions build failure

### Symptom
Both left and right builds failed during devicetree preprocessing with:

```
fatal error: ../zmk-nodefree-config/keypos_def/keypos_42keys.h: No such file or directory
```

### Root cause
ZMK GitHub Actions copies and mounts `config/` as `ZMK_CONFIG=/tmp/zmk-config/config`.
That means relative includes in `config/think_corney.keymap` cannot safely reference sibling directories outside `config/`, such as:

- `../zmk-nodefree-config/...`
- `../config_base/...`

Those paths exist in the repository tree, but not in the runtime layout used by the build job.

### Fix applied
The configuration was made self-contained inside `config/`:

- copied `config_base/config/base.keymap` to `config/base.keymap`
- copied `config_base/config/includes/*` to `config/includes/*`
- copied `zmk-nodefree-config/keypos_def/*` to `config/zmk-nodefree-config/keypos_def/*`
- changed `config/think_corney.keymap` includes to:

```c
#include "zmk-nodefree-config/keypos_def/keypos_42keys.h"
#include "base.keymap"
```

### Result
Both halves now avoid the missing-header failure caused by external relative paths from `ZMK_CONFIG`.

## v3 fixes
- Defined `nice_view_spi` explicitly in `boards/shields/think_corney/think_corney.dtsi` as a label on `&spi0` with proper pinctrl and CS pin. This fixes the `undefined node label 'nice_view_spi'` devicetree parse error on both halves.
- Fixed a malformed binding row in `config/base.keymap` (`&none &kp LG(V)`) that would have become the next keymap parse failure after the DTS issue.


## v4 fixes
- Replaced invalid reference override `&caps_word_capslock { ... }` with a real custom behavior node `caps_word_capslock: caps_word_capslock { compatible = "zmk,behavior-caps-word"; ... }`. Current ZMK officially supports creating additional caps-word instances this way.
- Removed obsolete `disable-on-keys` use from that block; upstream caps-word docs expose `continue-list`/`mods`, not `disable-on-keys`.
- Added `zmk-auto-layer` to `config/west.yml` because `HAS_UROB` + `&num_word` require that external module.
- Added `#include <behaviors/num_word.dtsi>` under `HAS_UROB` in `config/base.keymap` so the `&num_word` label is defined during keymap preprocessing.
