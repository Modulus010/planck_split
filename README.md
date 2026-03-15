# Planck Split

[![Build ZMK firmware](https://github.com/Modulus010/planck_split/actions/workflows/build.yml/badge.svg)](https://github.com/Modulus010/planck_split/actions/workflows/build.yml)

ZMK firmware configuration for a custom split Planck keyboard (4×6 per side, 48 keys total).

## Hardware

| Component | Specification |
|-----------|--------------|
| MCU | nRF52840 (integrated, not a dev board) |
| Layout | 4×12 split ortholinear (24 keys per side) |
| Key Scan | Direct GPIO (no diode matrix) |
| Encoders | Alps EC11 rotary encoder per side |
| LEDs | 3× PWM-driven LEDs per side |
| Battery | nRF VDDH battery monitoring |
| Connectivity | BLE 5.0 (central + peripheral split) + USB |
| Compatible | `yangxing,planck` |

## Layers

| # | Layer | Activation | Description |
|---|-------|-----------|-------------|
| 0 | BASE | Default | QWERTY with home row mods (GACS) |
| 1 | NAV | Hold: Space | Navigation, clipboard, caps word |
| 2 | MOUSE | Hold: Tab | Mouse movement and scrolling |
| 3 | MEDIA | Hold: Esc | Media controls, RGB, Bluetooth |
| 4 | NUM | Hold: Bksp | Number pad and arithmetic |
| 5 | SYM | Hold: Enter | Symbols |
| 6 | FUN | Hold: Del | Function keys (F1–F12) |

Layers also chain via top-left/top-right `&to` keys: BASE → NAV → MOUSE → MEDIA → NUM → SYM → FUN → BASE.

## Building

Firmware builds automatically via GitHub Actions on push to `config/` or `build.yaml`.

### Manual build (GitHub Actions)

1. Push changes to the repository
2. Go to **Actions** → **Build ZMK firmware**
3. Download the firmware artifacts (`planck_left` and `planck_right` UF2 files)

### Local build

```bash
# Clone with west
west init -l config
west update

# Build left half
west build -s zmk/app -b planck_left -- -DZMK_CONFIG="$(pwd)/config"

# Build right half
west build -s zmk/app -b planck_right -- -DZMK_CONFIG="$(pwd)/config"
```

## Flashing

1. Put the board into UF2 bootloader mode (double-tap reset)
2. Copy the `.uf2` file to the mounted drive:
   ```bash
   # macOS/Linux
   cp build/zephyr/zmk.uf2 /Volumes/NRF52BOOT/   # macOS
   cp build/zephyr/zmk.uf2 /media/$USER/NRF52BOOT/ # Linux
   ```
3. Flash the **left (central) half first**, then the right (peripheral)

## Repository Structure

```
├── config/
│   ├── west.yml                    # West manifest (ZMK dependency)
│   ├── planck.keymap               # Layer bindings
│   ├── planck.conf                 # Global Kconfig
│   ├── planck.json                 # Physical layout (for editors)
│   ├── include/
│   │   ├── behaviors.dtsi          # Hold-tap behaviors (home row mods, layer access)
│   │   ├── macros.dtsi             # Shift-function macros, BT selectors
│   │   └── layers.h                # Semantic layer name definitions
│   └── boards/arm/planck/          # Board definition (custom PCB)
│       ├── planck.dtsi             # Shared hardware: SoC, matrix, layout, sensors
│       ├── planck_left.dts         # Left half: GPIO pins, encoder, LEDs
│       ├── planck_right.dts        # Right half: GPIO pins, encoder, LEDs
│       ├── planck_{left,right}_defconfig  # Kconfig defaults per half
│       ├── leds_{left,right}.dtsi  # PWM LED definitions
│       └── Kconfig.*               # Board Kconfig fragments
├── build.yaml                      # ZMK build matrix
└── .github/workflows/build.yml     # CI build workflow
```

## Bluetooth

- 5 BT profiles (0–4) on the MEDIA layer
- Tap a profile key to select, Shift+tap to select and clear
- Enhanced TX power (+8 dBm) for range
- Experimental connection optimization enabled
- Central (left) fetches and proxies peripheral (right) battery level
