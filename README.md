# smartwork-for-eliot
Unofficial ESPHome &amp; Home Assistant integration for Eliot sit-stand desks, featuring smart reminders and BLE control.

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
