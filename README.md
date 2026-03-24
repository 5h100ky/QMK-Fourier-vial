# qmk-fourier-vial

QMK Vial firmware configuration for the Fourier 40% split keyboard — supports both **RP2040** (KEEBD) and **ATmega32U4** (Pro Micro) variants.

## Overview

This repository provides a complete [Vial](https://get.vial.today/)-compatible QMK keymap for the Fourier 40% V1.3 split keyboard, allowing real-time keymap editing via the Vial GUI.

**Key fix:** The `vial.json` files use the correct USB Vendor ID (`0x1666`) and Product IDs matching the keyboard hardware, so Vial can detect and display all keyboard layers correctly.

## File Structure

```
├── reset.uf2                  # RP2040 reset utility — re-enters BOOTSEL bootloader mode
├── rules.mk                   # Root build rules (intentionally blank)
├── rp2040/
│   ├── keyboard.json          # RP2040 hardware definition (matrix, pins, USB IDs, layout)
│   └── keymaps/
│       └── vial/
│           ├── config.h       # Vial UID and unlock combo (RP2040)
│           ├── keymap.c       # Keymap with 3 layers: BASE, FN1, FN2
│           ├── rules.mk       # Enables VIA and Vial (vendor serial for RP2040)
│           └── vial.json      # Vial GUI layout (VID 0x1666 / PID 0x0006)
└── atmega32u4/
    ├── keyboard.json          # ATmega32U4 hardware definition (Pro Micro pins, USB IDs)
    ├── rules.mk               # Build rules (intentionally blank)
    └── keymaps/
        └── vial/
            ├── config.h       # Vial UID and unlock combo (ATmega32U4)
            ├── keymap.c       # Keymap with 3 layers: BASE, FN1, FN2
            ├── rules.mk       # Enables VIA and Vial (bitbang serial for ATmega32U4)
            └── vial.json      # Vial GUI layout (VID 0x1666 / PID 0x0007)
```

## Layers

| Layer | Name  | Description                        |
|-------|-------|------------------------------------|
| 0     | BASE  | QWERTY base layer                  |
| 1     | FN1   | Numbers, RGB controls, navigation  |
| 2     | FN2   | Symbols, page/home navigation      |

## Building

Place this folder as a keyboard definition under your [vial-qmk](https://github.com/vial-kb/vial-qmk) installation:

```
keyboards/fourier_vial/
```

### RP2040 (KEEBD)

Compile with:

```bash
make fourier_vial/rp2040:vial
```

### ATmega32U4 (Pro Micro)

Compile with:

```bash
make fourier_vial/atmega32u4:vial
```

## Reset UF2

`reset.uf2` is a tiny RP2040 utility that immediately calls `reset_usb_boot(0, 0)`,
re-entering BOOTSEL (bootloader) mode without erasing any flash.

**Use case:** If you flash firmware that breaks USB (e.g. wrong build), copy `reset.uf2`
to the keyboard while it is in BOOTSEL mode. The keyboard will reboot back into BOOTSEL
so you can flash correct firmware.

**How to use:**
1. Hold the **BOOT** button (or short TP2 to GND) while plugging in USB — the keyboard
   appears as a USB mass-storage drive named `RPI-RP2`.
2. Copy `reset.uf2` onto that drive.
3. The keyboard immediately re-enters BOOTSEL mode (the drive reappears).
4. Flash your firmware `.uf2` normally.

## Hardware

### RP2040 (KEEBD)

- MCU: RP2040
- Split: UART serial via GP3
- Handedness pin: GP0
- RGB LEDs: 14 WS2812 (7 per side) via GP1
- USB VID: `0x1666` / PID: `0x0006`

**RP2040 GPIO mapping:**

| Function   | GPIO |
|------------|------|
| Row 0      | GP29 |
| Row 1      | GP6  |
| Row 2      | GP7  |
| Row 3      | GP8  |
| Col 0      | GP28 |
| Col 1      | GP27 |
| Col 2      | GP26 |
| Col 3      | GP22 |
| Col 4      | GP20 |
| Col 5      | GP23 |
| Col 6      | GP21 |
| Serial     | GP3  |
| WS2812 RGB | GP1  |
| Handedness | GP0  |

### ATmega32U4 (Pro Micro)

- MCU: ATmega32U4 (Pro Micro, Caterina bootloader)
- Split: bitbang serial via D0 (Pro Micro pin 3, SCL)
- Handedness pin: D2 (Pro Micro pin 0, RX) — wire HIGH on left half, LOW on right half
- RGB LEDs: 14 WS2812 (7 per side) via D3 (Pro Micro pin 1, TX)
- USB VID: `0x1666` / PID: `0x0007`

**Pro Micro pin mapping:**

| Function     | AVR Pin | Pro Micro Label |
|--------------|---------|-----------------|
| Row 0        | F4      | A3              |
| Row 1        | D7      | 6               |
| Row 2        | E6      | 7               |
| Row 3        | B4      | 8               |
| Col 0        | F5      | A2              |
| Col 1        | F6      | A1              |
| Col 2        | F7      | A0              |
| Col 3        | B1      | 15 (SCK)        |
| Col 4        | B3      | 14 (MISO)       |
| Col 5        | B2      | 16 (MOSI)       |
| Col 6        | B6      | 10              |
| Serial       | D0      | 3 (SCL)         |
| WS2812 RGB   | D3      | 1 (TX)          |
| Handedness   | D2      | 0 (RX)          |
