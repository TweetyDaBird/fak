---
title: Split keyboard support
---

### Split keyboard support

```markdown
## Split support

Central and peripheral sides are *fully independently* defined, so no considerations need to be made about symmetry, pin placement, and whatnot. They can be two entirely different keyboards connected together for all FAK cares.

```markdown
# This is an example of how a 10-key "split macropad" would be defined in keyboard.ncl

let { DirectPinKey, PeripheralSideKey, .. } = import "fak/keyboard.ncl" in
let { CH552T, .. } = import "fak/mcus.ncl" in

let D = DirectPinKey in
let S = PeripheralSideKey in

let side_periph = {
  mcu = CH552T,
  split.channel = CH552T.features.uart_30_31,
  keys = [
    D 13, D 14, D 15,
    D 32, D 33, D 12,
  ]
} in

# The central side has two fields that aren't in the peripheral:
# `split.peripheral` and `usb_dev`
{
  mcu = CH552T,
  split.channel = CH552T.features.uart_12_13,
  split.peripheral = side_periph,
  usb_dev = {
    # Nickel doesn't support hex literals yet
    vendor_id = 2023,
    product_id = 69,
    product_ver = 420,
  },
  keys = [
    D 14, D 15,   S 0, S 1, S 2,
    D 30, D 11,   S 3, S 4, S 5,
  ]
}

### Limitations:
- Only UART0 is supported. UART1 is not yet supported.
- Central and peripheral sides are fixed. That is, you can't plug it in on the peripheral side. Well, you can, but of course it won't work as a USB keyboard.

[^2]: Almost any pin. USB data pins obviously won't work here.

## Soft serial

This is similar to QMK's `SOFT_SERIAL_PIN` (bitbanged half-duplex UART). FAK offers this in order to preserve compatibility with split keyboards designed for QMK + Pro Micro. You may also use this to be able to use one more pin for keys, since hardware UART uses two pins, but this, only one pin.

For example, to use soft serial on pin 16: `split.channel = SoftSerialPin 16`. See also the provided [example keyboard definition](https://github.com/semickolon/fak-config/blob/main/keyboards/klor/keyboard.ncl) for [KLOR](https://github.com/GEIGEIGEIST/KLOR).

