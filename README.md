# ESP32 + TFT_eSPI (ILI9341) – PlatformIO Project

This is a PlatformIO-based ESP32 project using the **TFT_eSPI** graphics library to drive an **ILI9341-based TFT display**.

The project is intentionally structured so that:
- all hardware configuration lives in `platformio.ini`
- no library source files are modified
- the project can be cloned and built on a clean machine without manual setup steps

This repository is intended to be maintainable by my future self, or by my grandson Patrick (PJ).

---

## Hardware

### Microcontroller
- ESP32 Dev Module (`esp32dev`)

### Display
- TFT display using **ILI9341 controller**
- SPI interface

### Wiring

| TFT Signal | ESP32 Pin |
|-----------:|----------:|
| MOSI       | GPIO 16   |
| SCLK       | GPIO 17   |
| CS         | GPIO 2    |
| DC         | GPIO 4    |
| RST        | GPIO 19   |
| GND        | GND       |
| VCC        | 3.3V      |

**Notes:**
- Display runs at **3.3V logic**
- MISO is not used (write-only display)
- Backlight is assumed to be always on (no PWM control yet)

---

## Software Environment

### Tooling
- VS Code
- PlatformIO extension
- Arduino framework for ESP32

### Library Dependencies
- TFT_eSPI by Bodmer  
  Version constraint: bodmer/TFT_eSPI@^2.5.43

Libraries are downloaded automatically by PlatformIO — nothing is committed under `.pio/`.

---

## Display Configuration Strategy (Important)

**TFT_eSPI normally requires editing `User_Setup.h` inside the library.  
This project deliberately does *not* do that.**

Instead:
- All configuration is injected via compiler defines in `platformio.ini`
- The macro `USER_SETUP_LOADED` is defined to disable TFT_eSPI’s internal setup files
- This guarantees reproducible builds and avoids hidden configuration state

This approach was chosen so the project:
- works cleanly with Git
- survives library upgrades
- does not depend on include-order hacks

---

## platformio.ini (Key Section)

```ini
[env:esp32dev]
platform = espressif32
board = esp32dev
framework = arduino

lib_deps =
bodmer/TFT_eSPI@^2.5.43

build_flags =
-D USER_SETUP_LOADED
-D ILI9341_DRIVER
-D TFT_MOSI=16
-D TFT_SCLK=17
-D TFT_CS=2
-D TFT_DC=4
-D TFT_RST=19

## Building the Project

1. Install **VS Code**.
2. Install the **PlatformIO** extension.
3. Clone this repository.
4. Open the project folder in VS Code.
5. Select the `esp32dev` environment if prompted.
6. Click **Build** (✓).

PlatformIO will:

- fetch required libraries  
- apply the build flags  
- compile the project  

No manual library edits are required.

---

## Notes for Future Work

Possible extensions (not yet implemented):

- Explicit SPI frequency tuning
- Font selection via build flags
- Backlight PWM control
- Separation of display driver and application logic
- Integration with a state-machine framework (e.g. QP)

