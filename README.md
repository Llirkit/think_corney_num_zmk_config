# ThinkCorney ZMK config (nice!nano + nice!view + TrackPoint)

Что внутри:
- Shield: `think_corney_left` и `think_corney_right`
- Split: правая половина = CENTRAL (TrackPoint на правой половине)
- Дисплеи: nice!view на обеих половинах
- TrackPoint: PS/2 на правой половине (D1=CLK, D0=DATA)

## GitHub Actions
Файл `config/build.yaml` уже настроен на сборку 2х прошивок:
- `nice_nano_v2 + think_corney_left + nice_view`
- `nice_nano_v2 + think_corney_right + nice_view`

## Важно
- Если сборка ругнётся на `&spi1`, замени на `&spi0` в обоих overlay:
  `boards/shields/think_corney/think_corney_left.overlay` и `think_corney_right.overlay`.
