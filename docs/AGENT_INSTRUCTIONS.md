# AI Agent Instructions: Updating a Smart Hut ESPHome Smart Plug

These instructions are for AI agents (Gemini, ChatGPT, Claude, etc.) helping a non-technical user update their Smart Hut smart plug to the latest ESPHome firmware.

**Firmware repository:** https://github.com/Smart-Hut/Smart-Plug
**Latest config:** `github://Smart-Hut/Smart-Plug/ESPC2-02.yaml@v2.1.0`

**Important:** These plugs do not have a USB port for flashing. All updates are done over the air (OTA) via Wi-Fi.

## Before You Start

Confirm the user has:
- A Smart Hut ESPHome smart plug that is already powered on and connected to their Wi-Fi
- Home Assistant with the ESPHome integration installed, **or** standalone ESPHome access
- The plug must be online and reachable (check it responds in Home Assistant or the web server)

## Option A: Update via Home Assistant (Recommended)

This is the easiest path for most users.

### Step 1: Open the ESPHome Integration

1. Ask the user to open their **Home Assistant** dashboard.
2. Go to **Settings** > **Devices & Services**.
3. Find the **ESPHome** integration and click on it.
4. They should see a list of their ESPHome devices. Ask them to confirm the plug is listed and shows as **Connected**.

### Step 2: Add or Update the Device Configuration

If the plug is not yet configured in ESPHome using the Smart Hut package:

1. Ask the user to go to the **ESPHome** dashboard (accessible from the Home Assistant sidebar, or at `http://homeassistant.local:6052`).
2. Click the **three dots** menu in the top right and select **Import Configuration**.
3. Paste the following URL and click **Import**:
   ```
   github://Smart-Hut/Smart-Plug/ESPC2-02.yaml@v2.1.0
   ```
4. ESPHome will pull the latest configuration from GitHub.
5. Ask the user to click **Install** on the newly imported device, then select **Wirelessly (OTA)**.
6. Wait for the compilation and upload to complete. This may take several minutes.

If the plug is already configured:

1. Ask the user to open the device in the ESPHome dashboard.
2. Click the **pencil icon** (edit) to open the YAML configuration.
3. Ask them to replace or update the top of the file so the project version reads:
   ```yaml
   version: "2.1.0"
   ```
4. If they want the full latest configuration, they can paste the entire contents of:
   ```
   github://Smart-Hut/Smart-Plug/ESPC2-02.yaml@v2.1.0
   ```
5. Ask them to click **Install** and select **Wirelessly (OTA)**.

### Step 3: Verify the Update

1. After the upload completes, the plug will reboot automatically.
2. Ask the user to wait about 30 seconds.
3. In Home Assistant, ask them to find the plug and check the **ESPHome Version** diagnostic entity. It should show `2.1.0`.

## Option B: Update via ESPHome Dashboard (Standalone)

For users who run ESPHome as a standalone dashboard (not through Home Assistant).

### Step 1: Open the ESPHome Dashboard

1. Ask the user to open a browser and go to their ESPHome dashboard URL (commonly `http://localhost:6052` or `http://homeassistant.local:6052`).

### Step 2: Import the Latest Configuration

1. Click the **three dots** menu in the top right.
2. Select **Import Configuration**.
3. Paste:
   ```
   github://Smart-Hut/Smart-Plug/ESPC2-02.yaml@v2.1.0
   ```
4. Click **Import**.
5. When prompted, enter the plug's **encryption key** if required (this would have been provided when the plug was first set up).

### Step 3: Upload Wirelessly

1. Click **Install** on the device.
2. Select **Wirelessly (OTA)**.
3. Wait for compilation and upload to finish (several minutes).
4. The plug will reboot automatically.

### Step 4: Verify

1. Ask the user to check the **ESPHome Version** diagnostic entity in Home Assistant or the plug's web server.
2. It should show `2.1.0`.

## Troubleshooting

### The plug is not reachable for OTA
- The plug must be powered on and connected to the same network.
- Ask the user to check that the plug is listed as **Connected** in the ESPHome integration.
- If the plug is offline, they may need to power cycle it (unplug from the wall, wait 10 seconds, plug back in).

### OTA upload fails or times out
- This can happen on congested Wi-Fi networks. Ask the user to try again.
- If it keeps failing, the plug may need to be moved closer to the Wi-Fi router temporarily.

### The device is not in the ESPHome dashboard
- The user may need to add it manually. Ask them to click **Add Device** in the ESPHome dashboard and follow the prompts to adopt it.
- Alternatively, they can add it via the ESPHome integration in Home Assistant using the plug's IP address.

### The encryption key is unknown
- If the user does not know the encryption key, they will need to factory reset the plug.
- Factory reset: press and hold the physical button on the plug for 10+ seconds until the LED flashes rapidly.
- After reset, the plug will create a fallback Wi-Fi network called **Smart hut Plug**. The user can connect to it and reconfigure Wi-Fi, then re-adopt the device.

### The user does not have the ESPHome integration installed
- Ask them to go to **Settings** > **Add-ons** in Home Assistant and install the ESPHome add-on, or install the ESPHome integration via HACS.
- Once installed, the integration should auto-discover any ESPHome devices on the local network.
