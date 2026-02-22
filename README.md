# ThinkCorney ZMK config (nice!nano v2, split, central = right)

This repository contains:
- a custom ZMK shield definition for **ThinkCorney** (split keyboard)
- a starter keymap you can customize

## What gets built
- `think_corney_left` (peripheral)
- `think_corney_right` (central)

## Pinout (from your Ergogen nets)
Matrix is **col2row** with diodes from column -> row.

Columns (C0..C5): Pro Micro pins 21, 20, 19, 18, 15, 14  
Rows (R0..R4): Pro Micro pins 8, 4, 5, 6, 7

Row meaning:
- R0: num row
- R1: top row
- R2: home row
- R3: bottom row
- R4: thumb row

Thumb keys use row R4 and columns C3..C5.

## Building
### GitHub Actions
Push to GitHub and run the workflow that uses `build.yaml`.

### Local (example)
```sh
west build -b nice_nano_v2 -d build/left -- -DSHIELD=think_corney_left -DZMK_CONFIG="$(pwd)/config"
west build -b nice_nano_v2 -d build/right -- -DSHIELD=think_corney_right -DZMK_CONFIG="$(pwd)/config"
```

Then flash the resulting UF2 files to each nice!nano.



## Notes
- This repo includes `config/west.yml` so the upstream ZMK build workflow can `west init -l config`.
