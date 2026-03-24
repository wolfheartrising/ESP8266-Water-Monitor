# 📘 WaterMeter – ESPHome Pulse‑Based Water Meter

This project provides a **utility‑grade water‑usage monitor** using ESPHome, an ESP8266 (ESP‑01M), and a pulse‑output water meter. It converts raw pulses into **cubic feet**, **gallons**, **daily usage**, and **flow rate**, with full Home Assistant integration.

The configuration includes calibration logic, physical‑meter syncing, daily resets, and a hardware LED pulse indicator.

---

# 🧩 Bill of Materials (BOM)

| Component | Purpose | Notes |
|----------|---------|-------|
| **ESP8266 ESP‑01M (1MB)** | Main microcontroller running ESPHome | Small, low‑power, ideal for enclosure mounting |
| **A3144 Hall Effect Sensor Module** | Detects magnetic pulses from the water meter | Mounted externally on the meter body |
| **3.3V Regulated Power Supply** | Powers ESP‑01M and sensor | Must be stable; avoid cheap AMS1117 clones |
| **LED (3mm or 5mm)** | Visual pulse indicator | Connected to GPIO2 |
| **220–330Ω resistor** | LED current limiting | Required for GPIO2 LED |
| **10kΩ pull‑up resistor** | Stabilizes Hall sensor signal | Some modules include this onboard |
| **Wiring / Dupont leads** | Connections | Keep sensor wires short if possible |
| **Water meter with magnetic pulse output** | Source of pulses | Most residential meters have a magnet on the dial |

---

# 🔌 Wiring Overview

### ESP‑01M → Hall Sensor (A3144 Module)
- **GPIO4** → Hall sensor output  
- **3.3V** → Hall sensor VCC  
- **GND** → Hall sensor GND  
- External 10kΩ pull‑up recommended for stable pulses.

### ESP‑01M → LED Indicator
- **GPIO2** → LED anode (through 220–330Ω resistor)  
- LED cathode → **GND**

GPIO2 is safe for LED use on ESP‑01M and does not interfere with boot mode.

---

# 🚰 Features

### ✔️ Accurate pulse‑based metering
- Reads pulses from a Hall sensor on GPIO4  
- Converts pulses into:
  - Total cubic feet  
  - Total gallons  
  - Flow rate (ft³/min & gal/min)  
  - Daily usage (gallons, ft³, pulses)

### ✔️ Physical meter sync
A Home Assistant button recalculates the offset so ESPHome matches the physical meter’s cubic‑feet value.

### ✔️ Daily reset at midnight
Daily usage counters reset automatically using Home Assistant time.

### ✔️ LED pulse indicator
A small LED on GPIO2 blinks every **85 pulses** (~0.1 ft³) for visual confirmation.

### ✔️ Hardened pulse logic
- Prevents counter rollback  
- Clamps negative values  
- Stores offsets in flash for persistence  

### ✔️ Full Home Assistant integration
- Uses HA time  
- Reads physical meter value from an `input_number`  
- Exposes all sensors and controls automatically

---

# 📐 Calibration Values

This configuration uses:

- **849 pulses per cubic foot**  
- **0.001177 ft³ per pulse**  
- **0.00881 gallons per pulse**

These values can be adjusted if your meter differs.

---

# 🏠 Home Assistant Setup

Add this to your HA configuration
yaml
input_number:
  water_meter_physical:
    name: Physical Water Meter (ft³)
    min: 0
    max: 999999
    step: 0.001
