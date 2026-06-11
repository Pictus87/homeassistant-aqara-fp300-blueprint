# 🏠 Aqara FP300 – Home Assistant Blueprint

> Automated light and climate control powered by the Aqara FP300 mmWave presence sensor.

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://github.com/pictus87/ha-blueprint-aqara-fp300/blob/main/blueprints/aqara_fp300_automation.yaml)

---

## ✨ Features

### 💡 Light Control
- Turns light on when presence is detected **and** illuminance is below a configurable threshold
- **3 time-of-day profiles** (Morning / Day / Evening) with individual brightness and color temperature settings
- **Optional scene support** – call a Home Assistant scene instead of setting brightness/color temp directly
- **Weekday vs. Weekend profiles** – separate timing and light settings for Saturday/Sunday
- **Minimum on-duration** – prevents flickering on/off for brief walk-throughs
- Configurable **turn-off delay** after the room is vacated

### 🌡️ Climate Control *(optional)*
- Sets **comfort temperature** when presence is detected
- Separate comfort temperature for weekends
- **Setback temperature** after a short absence (configurable delay)
- **Eco temperature** after extended absence (configurable delay)

### 📍 Multi-Zone Support *(optional)*
- Up to **2 additional zones** (Zone 2 & Zone 3), each with:
  - Independent presence sensor (e.g. a specific FP300 sub-zone)
  - Independent light target
  - Individual turn-off delay
  - Optional scene per zone

---

## 📋 Requirements

| Component | Details |
|-----------|---------|
| Aqara FP300 | Integrated via Zigbee2MQTT, ZHA, or Aqara Hub |
| Presence entity | `binary_sensor` with `device_class: occupancy` |
| Illuminance entity | `sensor` with `device_class: illuminance` *(optional)* |
| Lights | Any `light` entity or group |
| Thermostat | Any `climate` entity *(optional)* |
| Home Assistant | 2023.6 or newer (Blueprint section input support) |

---

## 🚀 Installation

### Option A – One-Click Import
Click the **Import Blueprint** badge above.

### Option B – Manual
1. Copy `blueprints/aqara_fp300_automation.yaml` to your HA config folder:
   ```
   config/blueprints/automation/pictus87/aqara_fp300_automation.yaml
   ```
2. Restart Home Assistant or reload blueprints.
3. Go to **Settings → Automations → Blueprints** and create a new automation from this blueprint.

---

## ⚙️ Configuration Overview

| Section | Key Settings |
|---------|-------------|
| **1 · Sensors** | Presence sensor, illuminance sensor, lux threshold |
| **2 · Light – General** | Light target, turn-off delay, minimum on-duration, scene toggle |
| **3 · Weekday Profiles** | Morning / Day / Evening: start time, brightness, color temp, scene |
| **4 · Weekend Profiles** | Same as weekday, applied on Saturday & Sunday |
| **5 · Climate** | Enable toggle, thermostat entity, comfort/setback/eco temperatures and delays |
| **6 · Zone 2** | Second sensor + light with independent settings |
| **7 · Zone 3** | Third sensor + light with independent settings |

---

## 🔄 Automation Logic

```
Presence ON
 ├─ Lux below threshold?
 │   ├─ YES → Turn on light (scene or brightness/color temp based on time + weekday/weekend)
 │   └─ NO  → Skip light
 └─ Climate enabled? → Set comfort temperature

Presence OFF
 ├─ Wait: minimum on-duration (if configured)
 ├─ Wait: turn-off delay
 ├─ Still empty? → Turn off light
 └─ Climate enabled?
     ├─ Wait: setback delay → Set setback temperature
     └─ Wait: eco delay    → Set eco temperature
```

*Automation mode: `parallel` (max 4) – zones run independently.*  
*If presence returns during any delay, `restart` logic re-triggers the ON sequence.*

---

## 📝 License

MIT License – free to use, modify, and share.

---

## 🙋 Contributing

Pull requests and issues are welcome! If you have ideas for additional features, open an issue or submit a PR.
