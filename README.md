# DFRobot_MAX98357A — ESP32 compatibility patch

Patched copy of [DFRobot/DFRobot_MAX98357A](https://github.com/DFRobot/DFRobot_MAX98357A) for the original ESP32 (including ESP32-D0WD-V3) and MAX98357A amplifier. Original source attribution and license are retained.

## What changed

- Use the ESP-IDF 5 I2S channel API on Arduino-ESP32 3.x, retaining the legacy driver on 2.x.
- Preserve `begin(btName, bclk, lrclk, din)`, Bluetooth Classic A2DP/AVRCP, metadata, volume, filters and channel reversal.
- Keep all three `NUMBER_OF_FILTER` loop counters initialized to `i = 0`.
- Reserve Bluetooth Classic memory before `setup()` on recent cores. Without this declaration, Arduino-ESP32 3.3.11 can release the memory and initialization fails.
- Print the failed initialization step and ESP error to Serial.
- Adapt I2S clock changes, write timeout units and cleanup; use portable WAV field and FreeRTOS task types.

## Validation

The full `bluetoothAmplifier` sketch compiled for **ESP32 Dev Module** (`esp32:esp32:esp32`) with Arduino-ESP32 **3.3.11**. The user confirmed operation on hardware after the Bluetooth memory reservation fix.

The preceding I2S migration also compiled on **2.0.17**. The final startup diagnostic changes have not been recompiled on 2.0.17; the new memory-reservation header is conditionally included only when available. Other core versions and hardware have not been verified.

## Install

Download this repository as a ZIP and extract its contents into your Arduino sketchbook's `libraries/DFRobot_MAX98357A` directory, keeping a backup of any existing copy. Avoid installing duplicate copies of this library. Restart Arduino IDE if needed.

Open `examples/bluetoothAmplifier/bluetoothAmplifier.ino`, select **ESP32 Dev Module**, and use pins matching your wiring. The bundled example uses BCLK 25, LRCLK 26 and DIN 27. Avoid GPIO 1 for BCLK when using the default serial port, since GPIO 1 is UART TX.

This uses **Bluetooth Classic** and does not enable A2DP on ESP32-C3/S3 or other chips without Classic Bluetooth. Reinstalling the upstream library can overwrite these patches.

## Remaining limitations

- Bluetooth output retains the original fixed 44.1 kHz rate; negotiated 48 kHz input is not handled.
- Existing per-frame blocking writes, shared global state and metadata concurrency are unchanged.
- SD playback and malformed WAV error handling have not been validated or redesigned.
- Compiler warnings remain in original comments, unused variables, legacy initializers, and the deprecated Bluetooth device naming API.

See [upstream documentation](README_UPSTREAM.md) for the original API reference and [Chinese documentation](README_CN.md). The original [license](LICENSE) and Biquad source notices apply.
