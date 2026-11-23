# ESP32 Smart Digital Multimeter (UT33D+ Web Dashboard)

A wireless, smartphone-controlled **DIY Digital Multimeter** powered by an **ESP32** and a simple resistor voltage-divider network. It hosts a local Wi-Fi web server featuring a realistic **UT33D+ style rotary-dial interface** that lets you measure **DC Voltage**, **Resistance**, and **Continuity** directly from your mobile browser without any external LCD screen.

---
## Author

**Soumik Ghosh** — *TechTadka360*  
- YouTube: [@techtadka360official](https://www.youtube.com/@techtadka360official)  
- Instagram: [@techtadka360official](https://www.instagram.com/techtadka360official)  
- GitHub: [Mr-soumik](https://github.com/Mr-soumik)
- Facebook: [@techtadka360](https://www.facebook.com/share/1F3sa9eeeV/)
## Features

- **Realistic Multimeter Web UI:** Custom HTML/CSS/JS dashboard modeled after a classic digital multimeter with a clickable rotary dial, live LCD readout, **HOLD** button, and **Backlight** toggle.
- **DC Voltage Measurement:** Safely measures DC battery and circuit voltages (up to ~9.3V) using a calibrated `150kΩ + 82kΩ` voltage divider.
- **Auto-Ranging Ohmmeter:** Measures resistors from `0Ω` up to `200kΩ` (`Ω` and `kΩ` auto-switching) with multi-sample noise filtering and stability lock.
- **Audible Continuity Tester:** Real-time continuity detection with a `2.5kHz` buzzer alert and instant `000 Ω` / `OL` status display.
- **Zero External App Required:** Runs in SoftAP mode (`TechTadka360-Meter`)—connect via Wi-Fi and open `192.168.4.1` on any phone or PC.

---

## Components Required

| Component | Quantity | Notes |
| :--- | :---: | :--- |
| **ESP32 Dev Module** | 1 | Standard 30-pin or 38-pin ESP32 board |
| **10kΩ Resistor** | 1 | Reference pull-up (`R_REF`) for resistance/continuity |
| **82kΩ Resistor** | 2 | Series ADC protection (`R_S`) & lower divider (`R_BOT`) |
| **150kΩ Resistor** | 1 | Upper voltage divider (`R_TOP`) |
| **Buzzer (Active or Passive)** | 1 | Audible continuity alert |
| **Test Probes (Red & Black)** | 1 Pair | Red = Probe Node (`P`), Black = `GND` |
| **Breadboard & Jumper Wires** | 1 Set | For prototyping |

---

## Circuit Connections

All three measurement resistors (`10kΩ`, `82kΩ` series, and `150kΩ`) meet at a single common junction called the **Probe Node (`P`)**, which connects to the **Red Probe**. The **Black Probe** connects directly to **ESP32 GND**.

| ESP32 Pin | Connected Component | Purpose |
| :--- | :--- | :--- |
| **GPIO 25** | `10kΩ` Resistor $\rightarrow$ Probe Node (`P`) | Supplies `3.3V` reference in `Ω` / Continuity modes; `INPUT_PULLDOWN` in Voltage mode |
| **GPIO 33** | `82kΩ` Resistor $\rightarrow$ Probe Node (`P`) | ADC input for Resistance & Continuity sensing |
| **GPIO 34** | Junction of `150kΩ` (to `P`) & `82kΩ` (to `GND`) | ADC input for DC Voltage divider sensing |
| **GPIO 26** | Buzzer Positive (`+`) | Drives `2.5kHz` tone during continuity |
| **GND** | Buzzer (`-`), `82kΩ` (`R_BOT`), & Black Probe | Common ground reference |

---

## How It Works

1. **DC Voltage Mode (`V⎓`):** `GPIO25` is set to `INPUT_PULLDOWN` so it does not inject voltage into the probe node. Incoming DC voltage at the Red Probe passes through the `150kΩ` and `82kΩ` voltage divider (ratio $\approx 2.829$) and is read by `GPIO34`.
2. **Resistance Mode (`Ω`):** `GPIO25` outputs `3.3V` through the `10kΩ` resistor into the Probe Node. Because the `150kΩ + 82kΩ` (`232kΩ`) voltage divider remains in parallel with the unknown resistor $R_x$, the effective source resistance is calibrated to $R_{\text{equiv}} \approx 9586.8\Omega$. `GPIO33` reads the probe voltage and computes $R_x$.
3. **Continuity Mode (`•)))`):** `GPIO33` continuously monitors the probe voltage in the main loop. Touching the Red and Black probes together pulls the node low, immediately triggering the `2.5kHz` buzzer on `GPIO26`.

---

## Installation & Setup

1. Open **Arduino IDE** and ensure the **ESP32 Board Package (v3.x)** is installed.
2. Copy the code from `ESP32_Smart_Multimeter.ino` and paste it into a new sketch.
3. Select your **ESP32 Dev Module** board and the correct **COM Port**, then click **Upload**.
4. Once uploaded, open your smartphone's Wi-Fi settings and connect to:
   - **Wi-Fi SSID:** `TechTadka360-Meter`
   - **Password:** `techtadka360`
5. Open any mobile browser and navigate to **`http://192.168.4.1`**.
6. Tap the center rotary dial (or tap any mode label around the dial) to switch between **OFF**, **DC Voltage**, **Continuity**, and **Resistance**.

---

## Safety Warning

- **Do NOT measure AC mains voltage (110V/220V) or DC voltages exceeding 9.3V** with this resistor divider. Higher voltages will exceed the `3.3V` ADC limit on `GPIO34` and permanently damage the ESP32.
- Always disconnect external power sources before testing resistance or continuity.

---

## Author

**Soumik Ghosh** — *TechTadka360*  
- YouTube: [@techtadka360official](https://www.youtube.com/@techtadka360official)  
- Instagram: [@techtadka360official](https://www.instagram.com/techtadka360official)  
- GitHub: [Mr-soumik](https://github.com/Mr-soumik)
