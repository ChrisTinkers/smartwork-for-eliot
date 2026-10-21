# Installation guide

SmartWork for Eliot has two parts:

1. **ESPHome BLE desk control** — height monitoring and manual position buttons.
2. **Optional Home Assistant SmartWork package** — timed, actionable reminders.

> **Compatibility:** Developed and tested on one Eliot desk with a Jiecang **JCB35N2-230** controller and **JCP35N-BLT-V3-H** Bluetooth module, using ESPHome **2026.9.1**. Other hardware or firmware may behave differently. This is an unofficial community project.

## 1. Requirements

- A compatible Bluetooth-equipped desk and its original physical control panel.
- An ESP32 supported by ESPHome, powered and placed within reliable Bluetooth range of the desk.
- ESPHome Device Builder and a working Home Assistant installation.
- Your Wi-Fi credentials and the **Bluetooth MAC address of your own desk**.
- For reminders: the Home Assistant Companion App on a phone with working actionable notifications.

**Safety:** Clear the desk's entire travel path, keep its physical controls accessible, and test every command while supervising the desk. Home Assistant, ESPHome, and the BLE stop command are **not safety-rated emergency-stop systems**. Do not enable unattended automatic desk movement.

## 2. Identify the desk's BLE MAC address

Use an appropriate Bluetooth scanner (for example, an ESPHome BLE scanner or a phone BLE scanner) to identify the desk's Bluetooth module. Do not assume the MAC address shown in someone else's configuration will work. The device may not advertise its brand name clearly. Verify the address carefully before issuing any commands.

The tested protocol uses BLE service `FE60`, write characteristic `FE61`, and height characteristic `FE62`. These identifiers are **observations from the tested desk**, not a promise of compatibility with every Eliot/Jiecang model.

## 3. Install the ESPHome firmware

1. In **Home Assistant → ESPHome Device Builder**, create or open an ESP32 device.
2. Copy [`../esphome/eliot-desk.yaml`](../esphome/eliot-desk.yaml) into that device's configuration. Adjust `esphome.name` if you already have a device using that name.
3. Add the following entries to your **private ESPHome `secrets.yaml`** (usually accessible via ESPHome Device Builder's **Secrets** menu):

   ```yaml
   wifi_ssid: "YOUR_WIFI_NETWORK"
   wifi_password: "YOUR_WIFI_PASSWORD"
   eliot_ble_mac: "AA:BB:CC:DD:EE:FF"
   ```

   Replace all sample values with your own. The repository also contains [`../esphome/secrets.example.yaml`](../esphome/secrets.example.yaml) for reference. **Never upload your actual `secrets.yaml` to GitHub.**
4. Validate the configuration in ESPHome, then install/flash it to the ESP32 using the appropriate method for your device.
5. Add the ESPHome device to Home Assistant if it is not discovered automatically.
6. Confirm that the Bluetooth connectivity sensor reports connected and the height sensor reports a plausible value in centimetres.

The supplied firmware uses `esp32_ble.max_connections: 4` because the tested setup runs a Bluetooth proxy alongside the desk BLE client. This configuration has been tested on the stated hardware; adapt it carefully if your ESP32 setup differs.

## 4. Test manual control first

The ESPHome device exposes buttons named **Eliot Sitzen**, **Eliot Stehen**, **Eliot Laufband**, and **Eliot Stopp**, as well as height, BLE connectivity, and motion sensors.

1. Set up and verify **memory positions 1, 2, and 3 on the original desk keypad**. The firmware invokes those saved presets; it does not set absolute heights.
2. With the desk stationary and its travel path clear, test one position button at a time in Home Assistant.
3. Watch the height and motion sensors, and verify that the physical controls still work.
4. Test the stop function under supervision. Do **not** treat it as an emergency stop.

On the tested controller, the first position command sometimes only wakes the desk. The firmware waits briefly and **may retry once only if it has not detected movement and its safety checks still pass**. A short delay is therefore normal. Motion detection is inferred from height changes and is not a safety-rated detector.

**Do not proceed to notification-based control until manual operation is reliable.**

## 5. Install the optional Home Assistant package

1. Make a Home Assistant backup before editing its configuration.
2. Ensure packages are enabled in your Home Assistant `configuration.yaml`. If you do not already have a `homeassistant:` section, you can add:

   ```yaml
   homeassistant:
     packages: !include_dir_named packages
   ```

   If `homeassistant:` already exists, **add only the `packages:` key under it**; do not create a second top-level `homeassistant:` section. Do not overwrite any existing package configuration.
3. Copy [`../home-assistant/eliot-smartwork.yaml`](../home-assistant/eliot-smartwork.yaml) to `/config/packages/eliot-smartwork.yaml`.
4. Open **Developer Tools → States** in Home Assistant and identify the actual entity IDs created by your ESPHome device. Replace **every** `REPLACE_WITH_*` placeholder in the package:

   | Placeholder | Replace with |
   |---|---|
   | `binary_sensor.REPLACE_WITH_BLE_CONNECTION_ENTITY` | Desk Bluetooth connection sensor |
   | `sensor.REPLACE_WITH_DESK_HEIGHT_ENTITY` | Desk height sensor |
   | `button.REPLACE_WITH_STAND_BUTTON_ENTITY` | Stand preset button |
   | `button.REPLACE_WITH_SIT_BUTTON_ENTITY` | Sit preset button |
   | `notify.REPLACE_WITH_MOBILE_NOTIFY_ACTION` | Your working mobile notification action |
   | `device_tracker.REPLACE_WITH_HOME_PRESENCE_TRACKER` | Your phone or person tracker that reports `home` / `not_home` |

   Example notification action: `notify.mobile_app_my_iphone` (**example only**). Confirm the actual action under **Developer Tools → Actions** and send a test notification before continuing. Replace the complete action name in both notification branches; do not add a second `notify.` prefix.
5. Adjust the position thresholds in the package's template sensor. The sample logic is **below 95 cm = sitting; 95 to below 116 cm = standing; 116 cm and above = treadmill**. These thresholds reflect one user's desk and are **not universal**. Choose values that correctly classify your own presets.
6. Verify that your chosen presence tracker changes between `home` and `not_home` when you leave and return. SmartWork reminders and notification-triggered desk movement are blocked unless it reports `home`. Returning home resets the reminder timer after one minute. GPS home-zone presence is **not** proof that someone is sitting at the desk; a tracker may be stale or imprecise. This is an additional convenience safeguard, not a safety-rated interlock.
7. Review the default schedule and intervals in the package: **Monday–Friday, 08:00–16:00; 50 minutes sitting, 30 minutes standing, reminders every 10 minutes**. Edit them to suit your routine.
8. Run Home Assistant's configuration check and restart Home Assistant to load the package. Check logs for errors. The package is an **example template** and has not been independently validated on a clean Home Assistant installation; test the adapted configuration before relying on it.

## 6. Enable and test SmartWork

1. Find `input_boolean.eliot_smartwork_aktiv` under **Developer Tools → States** or add it as a toggle to a dashboard. Turn it **on** to enable reminders.
2. Confirm the template entity `sensor.eliot_smartwork_position` shows `sitzen`, `stehen`, or `laufband` as expected.
3. The daily reset automation runs at **08:00 on weekdays**. Position changes also update the next-reminder time. **If you enable SmartWork for the first time after 08:00, no initial reminder time is automatically created just by switching it on.** For an immediate controlled test, open **Developer Tools → Actions**, run `input_datetime.set_datetime` targeting `input_datetime.eliot_smartwork_naechste_erinnerung`, and set its date/time to a few minutes from now during the configured active hours. Alternatively, wait for the next weekday's 08:00 reset. A real position change can also schedule a reminder.
4. Verify that a notification arrives on your phone. Available actions include **Jetzt stehen / Jetzt sitzen**, **In 10 Minuten**, and **Heute nicht** (wording may depend on the package version).
5. Test each action with the desk supervised. **Only explicit user actions should initiate desk movement**; reminders themselves do not move it.

The template checks home-zone presence but does **not** detect whether a person is present at the desk. **LD2410C mmWave presence detection is planned, not implemented.**

## 7. Troubleshooting

| Symptom | Things to check |
|---|---|
| BLE sensor stays disconnected | Desk powered on; correct BLE MAC; ESP32 within range; competing Bluetooth connections |
| No valid height | BLE connection and characteristic notifications; tested controller compatibility; wait for the initial read |
| Button does nothing | Connection and height valid; desk stationary; memory preset configured; inspect ESPHome logs |
| Movement appears late or not at all | Height notifications arriving; movement is estimated from height changes, not a dedicated hardware switch |
| No reminders | SmartWork enabled; weekday/time window; no daily pause; home presence tracker is `home`; BLE connected; position classified correctly; next-reminder timestamp |
| Notification action does nothing | Correct `notify.mobile_app_*` service; Companion App notification permissions; correct button entity IDs; action conditions and active hours |

## 8. Limitations and contributions

This release is based on one successfully tested desk setup, **not** broad hardware validation. Bluetooth protocol behaviour, preset mappings, and notification support may vary. Contributions and hardware compatibility reports are welcome; include your controller/module model and ESPHome version, but **remove MAC addresses, Wi-Fi credentials, tokens, and personal information** from logs.

This project is independent and unofficial; it is not affiliated with or endorsed by Eliot or Jiecang. See the repository's MIT license and safety disclaimer.
