# ThinkCorney ZMK config

This repository is self-contained and does not require Git submodules to build.

## Firmware targets

The build matrix produces firmware for:
- `think_corney_left + nice_view`
- `think_corney_right + nice_view`

using the current `nice_nano@2.0.0` board naming used by modern ZMK/Zephyr.

## Layout notes

- Personal user config is in `config/`
- Custom shield files are in `boards/shields/think_corney/`
- A compatibility mirror also exists in `config/boards/shields/think_corney/` for toolchains still expecting that search path
- Shared keymap/include assets from the former `config_base` submodule are vendored directly into this repo
