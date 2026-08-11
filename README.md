# Smart Hut Smart Plug ESPHome Firmware

This repository contains the ESPHome firmware configuration for Smart Hut power-monitoring smart plugs based on the ESP32-C3.

If you bought a pre-flashed plug from [Smart Hut](https://thesmarthut.com/), this is the firmware it is designed to run. If you are building your own setup or recovering a device, this repo also gives you the hardware reference you need.

## What This Firmware Gives You

Out of the box, the firmware is designed to be useful without needing to edit YAML.

Main features:
- Relay control from Home Assistant
- Power, voltage, current, and energy monitoring
- Daily and lifetime energy tracking
- Daily and lifetime cost estimates
- Configurable power-loss behavior after a reboot or outage
- Configurable power restore delay
- Local button controls with single, double, and long press actions
- Configurable LED behavior
- Auto-off timer
- Standby killer mode for low-power idle loads
- Appliance run and finish detection
- Calibration controls for voltage, current, and power readings
- Diagnostics including Wi-Fi signal, uptime, IP address, SSID, MAC address, and ESPHome version
- Web server support
- ESPHome OTA updates
- `esp32_improv` support for easier provisioning

## Getting Started

### 1. Add The Device To ESPHome

The easiest route is to add the device through ESPHome in Home Assistant using this package URL:

```text
github://Smart-Hut/Smart-Plug/ESPC2-02.yaml@v2.0.0
```

If you are importing this package into your own config, ESPHome will pull in the device settings from this repository.

### 2. Power On The Plug

On first boot, the plug will create a fallback access point if it cannot connect to Wi-Fi.

Fallback AP name:

```text
Smart hut Plug
```

From there, you can provision Wi-Fi and finish onboarding it into ESPHome and Home Assistant.

### 3. Add It To Home Assistant

Once the device is online, Home Assistant should discover it through the ESPHome integration. After adding it, you will get a switch for the relay plus the monitoring, automation, and diagnostic entities exposed by the firmware.

### 4. Configure The Features You Want

Most of the behavior is controlled using Home Assistant entities created by the firmware.

Useful first settings to review:
- `Power loss behaviour`
- `Power Restore Delay`
- `LED Behaviour`
- `Auto Off Enabled`
- `Auto Off Minutes`
- `Electricity Rate`

## Everyday Use

### Button Actions

The physical button on the plug supports multiple actions:
- Single press: toggle the relay
- Double press: cycle `Power loss behaviour`
- Long press: cycle `LED Behaviour`

If you do not want the local button to change anything, enable `Local Button Lock`.

### Power Loss Behavior

The plug can be configured to do one of three things after power returns:
- `OFF`: always come back off
- `ON`: always come back on
- `Restore last state`: return to whatever state the relay had before power was lost

You can also set `Power Restore Delay` if you want the relay action to wait a few seconds after boot.

### LED Behavior

You can choose how the status LED behaves:
- `Relay state`: LED follows whether the plug is on or off
- `Always off`: LED stays off
- `Always on`: LED stays on

## Built-In Features

### Energy Monitoring

The firmware exposes these main measurements:
- `Voltage`
- `Current`
- `Power`
- `Energy`
- `Daily Energy`
- `Lifetime Energy`

This makes the plug suitable for both live monitoring and longer-term tracking in Home Assistant dashboards.

### Cost Tracking

The firmware can estimate running cost using the `Electricity Rate` helper.

Available cost sensors:
- `Today Cost`
- `Lifetime Cost`

Set `Electricity Rate` to your local price in `GBP/kWh`.

### Auto-Off Timer

If `Auto Off Enabled` is turned on, the relay will turn off automatically after the time set in `Auto Off Minutes`.

This is useful for heaters, chargers, and anything you do not want left on indefinitely.

### Standby Killer

`Standby Killer Enabled` watches the live power reading and can turn the plug off after a device has dropped into a low-power standby state.

Relevant controls:
- `Standby Threshold`
- `Standby Killer Delay`

This is useful for TVs, media devices, and chargers that sit idle drawing a small amount of power.

### Appliance Detection

`Appliance Detection Enabled` is intended for devices like washing machines, tumble dryers, or dishwashers where power usage shows a clear run cycle.

It exposes these status sensors:
- `Appliance Running`
- `Appliance Finished`

Relevant controls:
- `Appliance Running Threshold`
- `Appliance Finished Threshold`
- `Appliance Finished Delay`

Typical behavior:
1. The plug detects when power rises above the running threshold.
2. It marks the appliance as running.
3. When power later falls below the finished threshold for long enough, it marks the appliance as finished.

### Threshold And Detection Helpers

The firmware also provides these helper binary sensors:
- `Load Detected`
- `Standby Detected`
- `High Power`

These can be useful in Home Assistant automations even if you do not use the built-in standby or appliance features directly.

### Calibration

If your readings need fine tuning, you can adjust them in Home Assistant using:
- `Voltage Calibration Multiplier`
- `Current Calibration Multiplier`
- `Power Calibration Multiplier`

These are intended for final adjustment after comparing the plug against a trusted meter.

### Diagnostics

For troubleshooting and maintenance, the firmware exposes:
- `WiFi Signal`
- `Uptime`
- `IP Address`
- `Connected SSID`
- `MAC Address`
- `ESPHome Version`
- Restart button
- Factory reset restart button

## Advanced Notes

### OTA Updates

The firmware supports ESPHome OTA updates, so once the device is online you can keep it updated without opening the plug.

### API Compatibility

This firmware is compatible with newer ESPHome releases that removed the old `api.password` option. If you use a full custom config and want secure API access, add API encryption in your own config rather than relying on the removed password field.

## Hardware Reference

These are the pinouts for the plug if you want to build your own config, inspect the hardware, or port it to another firmware.

### BL0937

Used for energy monitoring.

| Pin | Function |
| ---- | ---- |
| GPIO5 | SEL |
| GPIO7 | CF |
| GPIO3 | CF1 |

### Plug

| Pin | Function |
| ---- | ---- |
| GPIO20 | Button |
| GPIO6 | LED |
| GPIO4 | Relay |

## Tasmota Template

If you are experimenting with alternative firmware, the original Tasmota template is kept here for reference:

```json
{"NAME":"Smart Hut PM","GPIO":[0,0,0,2656,224,2624,320,2720,0,0,0,0,0,0,0,0,0,0,0,0,32,1],"FLAG":0,"BASE":1}
```
