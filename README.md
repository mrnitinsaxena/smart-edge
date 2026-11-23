<p align="center">
  <picture>
    <img src="static/images/smart-edge-logo.svg" alt="Smart Edge" width="440">
  </picture>
</p>

<h1 align="center">Pro Sensor</h1>

<p align="center">
  <a href="https://mrnitinsaxena.github.io/smart-edge/"><b>Install firmware</b></a> ·
  <a href="https://github.com/mrnitinsaxena/smart-edge/releases">Releases</a>
</p>

ESPHome firmware for the **Smart Edge Pro Sensor** by [Resonate to Innovate](https://resonatetoinnovate.com/): an ESP32-based room sensor with temperature, humidity and light sensing, optional mmWave presence detection, IR remote control and a status LED with a buzzer. Built for [Home Assistant](https://www.home-assistant.io/).

## Features

- **Environment:** temperature and humidity (AHT20) and illuminance (BH1750), each with an adjustable calibration offset
- **Presence (optional):** works with the HLK-LD2410, LD2412, LD2420 and LD2450, DFRobot SEN0395 and SEN0609, and Seeed MR24HPC1 mmWave radars
- **IR control (optional):** Samsung and Sony TVs, Daikin and Panasonic ACs, Atomberg fans
- **Alerts:** a high-temperature alert with an adjustable threshold, a siren and an emergency LED pattern; follows Alarmo automatically, and an **External alarm** switch lets any Home Assistant automation sound the siren
- **Status feedback:** RGB LED and buzzer for boot, Wi-Fi and Home Assistant connection, with a Do Not Disturb switch
- **Easy setup:** Wi-Fi over Bluetooth (Improv) or a captive portal; adopt in Home Assistant and the ESPHome dashboard
- **Secure by default:** no shared passwords or keys; Home Assistant sets the API encryption key when it adopts the device

## Getting started

1. **Install the firmware** (skip if your sensor came pre-installed): open the [web installer](https://mrnitinsaxena.github.io/smart-edge/) in Chrome or Edge, connect the sensor by USB and click **Install firmware**.
2. **Power the device** from a 5 V USB supply. It beeps once and the LED pulses green.
3. **Connect it to Wi-Fi** in one of two ways:
   - **Bluetooth:** open the Home Assistant app, or [improv-wifi.com](https://www.improv-wifi.com/) in Chrome, and select the device.
   - **Hotspot:** join the `pro-sensor-xxxxxx` Wi-Fi network from your phone. The setup page opens automatically.

   Two beeps and a blue LED mean it's online.
4. **Add it to Home Assistant.** It's discovered automatically under *Settings → Devices & services*. Click **Configure**. Three beeps mean Home Assistant has connected.

### Customize the firmware (optional)

In the ESPHome dashboard the device shows up as **Discovered**. Click **Adopt** to take over its configuration, then add any of the options below.

You can also start from scratch:

```yaml
packages:
  smart_edge: github://mrnitinsaxena/smart-edge/pro-sensor.yaml@v2026.11.0

substitutions:
  ld2410: true            # enable the presence sensor you fitted
  sensor_mode_uart: true

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password
```

## Configuration options

Set any of these under `substitutions:` in your device config.

### Presence sensors (enable one)

| Option | Sensor |
|---|---|
| `ld2410: true` | HLK-LD2410 |
| `ld2412: true` | HLK-LD2412 |
| `ld2420: true` | HLK-LD2420 |
| `ld2450: true` | HLK-LD2450 |
| `sen0395: true` | DFRobot SEN0395 |
| `sen0609: true` | DFRobot SEN0609 |
| `mr24hpc1: true` | Seeed MR24HPC1 |
| `sensor_mode_uart: true` | Use UART (full data) instead of the GPIO output (presence only) |

### IR remotes

| Option | Device |
|---|---|
| `ir_samsung_tv: true` | Samsung TV |
| `ir_sony_tv: true` | Sony TV |
| `ir_daikin_ac: true` | Daikin AC |
| `ir_panasonic_ac: true` | Panasonic AC |
| `ir_atomberg_fan: true` | Atomberg fan |

### Device and network

| Option | Default | Description |
|---|---|---|
| `custom_device_name` | `pro-sensor-xxxxxx` | Hostname |
| `custom_friendly_name` | `Pro Sensor` | Name shown in Home Assistant |
| `custom_area` | — | Room suggested to Home Assistant when the device is first added (e.g. `Guest Room`). Keep the room out of the name to avoid IDs like `guest_room_guest_room_sensor_…` |
| `wifi_ssid` / `wifi_password` | — | Hard-code Wi-Fi instead of using onboarding |
| `api_key` | provisioned by HA | Fix the API encryption key (also encrypts OTA) |
| `web_username` / `web_password` | off | Enable the built-in web page (both required) |
| `improv_ble` | `true` | Set `false` to disable Bluetooth Wi-Fi setup |
| `wifi_power_mode` | `NONE` | `NONE`, `LIGHT` or `HIGH` power saving |
| `cpu_frequency_mode` | 240 MHz | `LIGHT` = 160 MHz; any other value = 80 MHz |
| `timezone` | `Asia/Kolkata` | IANA timezone |
| `logger_level` | `WARN` | ESPHome log level |
| `usb_logs` | `true` | Logs over the USB cable (115200 baud); set `false` to turn off |

### Alerts and LEDs

| Option | Default | Description |
|---|---|---|
| `alarm_panel_entity` | `alarm_control_panel.alarmo` | Home Assistant alarm panel that triggers the siren when it goes off |
| `alarm_panel` | `true` | Set `false` to stop following an alarm panel |
| `external_ws2812_pin` | onboard | GPIO for an external WS2812 strip |
| `external_ws2812_num_led` | `1` | Number of LEDs on the external strip |

Set the **Power supply** option in Home Assistant to match your adapter. The LED brightness is capped to stay within that budget.

## Status signals

| Event | LED | Buzzer |
|---|---|---|
| Booted | Green pulse | 1 beep |
| Wi-Fi connected | Blue pulse | 2 beeps |
| Home Assistant connected | Off | 3 beeps |
| Wi-Fi / HA lost | Fast pulse | Long tone |
| Alert | Red/blue flashing | Siren |

**Do Not Disturb** (off by default) silences the connection sounds and LEDs. Alerts always sound.

## Alarms

- **Alarmo:** if you use [Alarmo](https://github.com/nielsfaber/alarmo), the sensor sounds its siren automatically whenever the alarm is triggered.
- **Anything else:** use the **External alarm** switch. For example, create an automation: *when the smoke detector or water leak sensor turns on, turn on Pro Sensor External alarm*. Turn the switch off to silence it.

## Choosing your IR remotes

Under the device's **Configuration** in Home Assistant, set **TV remote**, **AC remote** and **Fan remote** to your brands. The device restarts and only shows the controls for the brands you chose. If you switch brands later, update any automations that used the old controls.

## Factory reset

- **Without Home Assistant:** unplug and replug the power **5 times**, with each replug within 10 seconds. The device erases its Wi-Fi and API key and restarts in setup mode.
- **From Home Assistant:** press the **Factory reset** button under the device's configuration entities.

## Hardware

| Function | GPIO |
|---|---|
| I²C SDA / SCL (AHT20, BH1750) | 21 / 22 |
| Buzzer | 23 |
| WS2812 status LED | 25 |
| IR receiver | 18 |
| IR transmitter | 19 |
| Radar UART RX / TX | 16 / 17 (SEN0609: 17 / 16) |
| Radar GPIO output | 26 (SEN0395: 27; MR24HPC1 S1/S2: 26 / 27) |

MCU: ESP32 (rev 3.1+), ESP-IDF framework.

## ⚠️ Safety notice

The high-temperature alert is a convenience feature. It is **not** a certified smoke or fire alarm and must not replace one. Always install certified smoke detectors.

## License

MIT. See [LICENSE](LICENSE).

---

<p align="center">
  Designed and built by<br><br>
  <a href="https://resonatetoinnovate.com/">
    <picture>
      <img src="static/images/resonate-to-innovate-logo.svg" alt="Resonate to Innovate" width="240">
    </picture>
  </a>
  <br>
  <a href="https://resonatetoinnovate.com/">resonatetoinnovate.com</a>
</p>
