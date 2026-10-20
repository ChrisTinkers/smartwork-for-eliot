# SmartWork for Eliot

**Unofficial ESPHome and Home Assistant integration for compatible Eliot sit-stand desks.**

Control your desk via Bluetooth Low Energy (BLE), monitor its height, and receive gentle reminders to alternate between sitting and standing.

Built for everyday use: smart, practical, and unobtrusive.

[![Buy Me a Coffee](https://img.shields.io/badge/Buy%20Me%20a%20Coffee-Support%20ChrisTinkers-FFDD00?logo=buymeacoffee&logoColor=black)](https://buymeacoffee.com/christinkers)

## Features

- **Bluetooth desk control** using an ESP32 and ESPHome
- **Live desk height monitoring** in Home Assistant
- **Three memory positions** for sitting, standing, and treadmill use
- **One-click position control** with intelligent retry logic
- **Movement detection** based on height changes
- **Stop command** for desk movement
- **SmartWork reminders** with configurable sitting and standing intervals
- **Interactive mobile notifications** to move the desk, snooze reminders, or pause for the day
- **Manual control first:** reminders never move the desk without user interaction

## How it works

An ESP32 connects directly to the desk's Bluetooth controller using ESPHome.

Home Assistant receives the desk height and movement status and provides buttons for controlling saved desk positions.

An optional SmartWork automation monitors the desk's position and sends reminders when it may be time to change posture.

The desk only moves when explicitly requested.

## Tested hardware

This project was developed and tested with:

| Component | Hardware |
|---|---|
| Desk | Eliot electric sit-stand desk |
| Controller | Jiecang JCB35N2-230 |
| Bluetooth module | JCP35N-BLT-V3-H |
| Microcontroller | ESP32 |
| Firmware | ESPHome 2026.9.1 |
| Smart home platform | Home Assistant |

Other models and firmware versions may behave differently.

## Project status

**Early community release — tested in daily use on one desk.**

The core functionality has been tested successfully, including BLE communication, height monitoring, desk movement, and SmartWork notifications.

Compatibility with additional Eliot and Jiecang controllers has not yet been verified.

## Roadmap

### Planned: Presence Detection

A future version of SmartWork is planned to include optional presence detection using an **LD2410C mmWave radar sensor** connected to the ESP32.

The goal is to make reminders even more context-aware:

- Detect whether someone is actually at the desk.
- Pause sitting and standing timers when the desk is unattended.
- Resume reminders when the user returns.
- Avoid unnecessary notifications during breaks or meetings away from the desk.
- Explore a compact, 3D-printed enclosure combining the ESP32 and radar sensor.

**Current status:** Planning stage. Not yet implemented or tested.

Presence detection will remain optional. The existing reminder system will continue to work without additional sensors.

## Installation

SmartWork for Eliot consists of two components:

1. **ESPHome BLE integration** — Connects an ESP32 to your desk, provides live height monitoring, movement detection, and buttons for controlling saved desk positions.
2. **Home Assistant SmartWork package** — Adds configurable sitting and standing reminders with interactive mobile notifications.

### Quick Start

1. Prepare an ESP32 with ESPHome.
2. Configure your desk's Bluetooth MAC address and Wi-Fi credentials.
3. Install the [ESPHome configuration](esphome/eliot-desk.yaml).
4. Verify that height monitoring and desk controls work correctly.
5. Optionally install the [Home Assistant SmartWork package](home-assistant/eliot-smartwork.yaml).
6. Configure your own entity IDs, mobile notification service, working hours, and reminder intervals.

**[📖 Read the full installation guide](docs/installation.md)**

The installation guide includes hardware requirements, configuration instructions, initial testing, and troubleshooting.

> **Note:** This project has currently been tested with one Eliot desk using a Jiecang JCB35N2-230 controller and JCP35N-BLT-V3-H Bluetooth module. Compatibility with other models is not guaranteed.

## Safety

This project controls a motorized, height-adjustable desk.

- Always keep the desk's movement area clear.
- Keep the original physical controls accessible.
- Do not rely on the ESP32 or Home Assistant as a safety system.
- Test each command carefully before using it in automations.
- Bluetooth communication and behavior may differ between controller versions.

The software is provided without warranty. Use it at your own risk.

## Disclaimer

This is an independent, unofficial open-source project. It is not affiliated with, endorsed by, or sponsored by Eliot, Jiecang, or their respective manufacturers.

All trademarks belong to their respective owners.

## Support this project ☕

If SmartWork for Eliot has helped you, you can [buy me a coffee](https://buymeacoffee.com/christinkers). Contributions are entirely optional and help support future DIY electronics and open-source smart home projects. The project remains free and open source.

## License

This project is licensed under the MIT License.

---

Made by [ChrisTinkers](https://github.com/ChrisTinkers).

*Useful technology for everyday life.*
