AI Agent Instructions: Updating and Troubleshooting a Smart Hut ESPHome Smart Plug

These instructions are for AI assistants such as ChatGPT, Gemini, Claude, Copilot, and similar systems helping a user update or troubleshoot a Smart Hut ESPHome Power Monitoring Smart Plug.

The user may be non-technical. Do not assume they understand ESPHome, Docker, Home Assistant installation types, OTA protocols, networking, YAML, or firmware compilation.

Your job is to identify their environment, choose the simplest valid update method, and guide them through it without inventing technical limitations that do not exist.

⸻

Authoritative Firmware Source

The official Smart Hut firmware repository is:

https://github.com/Smart-Hut/Smart-Plug

The primary ESPHome configuration is:

ESPC2-02.yaml

Raw current configuration:

https://raw.githubusercontent.com/Smart-Hut/Smart-Plug/main/ESPC2-02.yaml

Always treat the repository as the authoritative source.

Do not reconstruct the Smart Hut firmware from memory.

Do not substitute a generic ESP32 smart-plug configuration.

Do not use an Athom configuration or another manufacturer’s configuration simply because the hardware appears similar.

Do not assume a firmware version number from this document is still current.

Before instructing the user to update, obtain or refer to the current ESPC2-02.yaml from the Smart Hut repository.

⸻

Critical Facts About Smart Hut ESPHome Plugs

Smart Hut ESPHome smart plugs based on this firmware are ESP32-C3 devices.

The firmware includes native ESPHome OTA support:

ota:
  - platform: esphome

Therefore, a plug which is already running Smart Hut ESPHome firmware and connected to the network can normally be updated wirelessly using ESPHome.

The firmware also provides a web server, but the web server and ESPHome OTA are different services.

The presence or absence of a firmware upload page in the web interface does not determine whether ESPHome OTA is available.

In particular:

* A missing browser Choose File button does not mean OTA is unavailable.
* http://DEVICE_IP/update failing does not mean OTA is unavailable.
* ERR_EMPTY_RESPONSE from /update does not prove OTA is missing.
* ESPHome OTA does not require an HTTP /update endpoint.
* ESPHome OTA does not require a USB port.
* ESPHome OTA does not require Home Assistant OS.
* ESPHome OTA does not require Home Assistant Supervisor.
* ESPHome OTA does not require the ESPHome Home Assistant add-on.
* ESPHome OTA does not require the device to appear in an ESPHome Dashboard.
* Home Assistant itself does not compile ESPHome firmware.

Never conclude that a plug is structurally incapable of OTA merely because its web interface has no firmware upload control.

⸻

Bluetooth Proxy

Current Smart Hut firmware may include ESPHome Bluetooth Proxy support.

To determine whether the current firmware supports it, inspect the current ESPC2-02.yaml from the repository.

For example, Bluetooth Proxy is enabled when the configuration contains:

bluetooth_proxy:
  active: true

Do not tell a user their plug is incapable of Bluetooth Proxy merely because the currently installed firmware does not expose Bluetooth functionality in Home Assistant.

An older firmware version may simply need updating.

⸻

Important Terminology

Do not confuse the following components.

Home Assistant ESPHome Integration

The ESPHome integration allows Home Assistant to communicate with ESPHome devices.

It appears under:

Settings → Devices & Services → ESPHome

The integration does not compile firmware.

The integration does not provide the ESPHome Dashboard.

The integration is built into Home Assistant and is not normally installed through HACS.

ESPHome Dashboard / Device Builder

ESPHome provides the firmware compiler, configuration manager, logs, and OTA uploader.

It can be run:

* as the ESPHome Device Builder add-on under Home Assistant OS/Supervised;
* as its own Docker container;
* as a standalone Python/CLI installation;
* in another supported ESPHome environment.

ESPHome OTA

ESPHome OTA is the protocol used to upload firmware wirelessly to an existing ESPHome device.

It is independent of the device’s HTTP web server.

ESPHome API

The ESPHome API is primarily used for communication between the device and Home Assistant or other API clients.

API encryption and OTA authentication are separate concepts.

An API encryption problem does not automatically mean OTA is impossible.

⸻

First: Determine the User’s Environment

Before giving UI-specific instructions, establish which environment the user has.

Typical environments are:

1. Home Assistant OS or Home Assistant Supervised
    * User normally has an Add-ons section.
    * ESPHome Device Builder may be installed as an add-on.
2. Home Assistant Container / Docker
    * Home Assistant runs as a Docker container.
    * There is no Supervisor.
    * There is normally no Home Assistant Add-ons menu.
    * ESPHome can be run as a separate Docker container.
3. Home Assistant Core
    * No Supervisor/add-on system.
    * ESPHome can be run separately.
4. Standalone ESPHome
    * User already has ESPHome Dashboard or CLI independently of Home Assistant.
5. No ESPHome compiler currently installed
    * Use the Docker method where Docker is available.
    * Otherwise help the user install ESPHome using an appropriate supported method.

Do not assume that a user running Home Assistant also has the ESPHome Dashboard.

⸻

Before Updating

Confirm as many of the following as practical:

* The plug is powered.
* It is connected to Wi-Fi.
* It currently works in Home Assistant and/or its web page is reachable.
* Its IP address is known, if possible.
* The computer or NAS running ESPHome can reach the same network as the plug.
* The plug is a Smart Hut ESPHome model rather than the Smart Hut Tasmota model.

If the plug is currently reachable at an IP address, use that IP address for OTA where possible.

Using the IP directly avoids unnecessary mDNS .local troubleshooting.

Example:

192.168.0.119

⸻

Recommended Update Decision Tree

Use this order.

Path A — User Has ESPHome Device Builder / Dashboard

Use the existing ESPHome environment.

Path B — User Runs Home Assistant in Docker

Run ESPHome in a separate Docker container.

This is a fully supported architecture.

Do not tell the user that Home Assistant Container prevents them from updating ESPHome devices.

Path C — User Already Has ESPHome CLI

Download the current Smart Hut YAML and use the CLI directly.

Path D — ESPHome OTA Actually Fails

Diagnose the OTA failure.

Do not jump directly to physical flashing, factory reset, return, replacement, or declaring the device unrecoverable.

⸻

Path A: ESPHome Dashboard / Device Builder

If the user already has a working ESPHome Dashboard, use it.

First obtain the current Smart Hut configuration from:

https://github.com/Smart-Hut/Smart-Plug

The configuration file is:

ESPC2-02.yaml

If the Dashboard supports importing from GitHub, the configuration can be obtained from the Smart Hut repository.

If using a package URL, prefer the version specified by the repository’s current documentation/configuration rather than copying an old version number from another source.

Once the configuration is available:

1. Compile the firmware using ESPHome.
2. Select the wireless/OTA installation method.
3. Target the existing Smart Hut plug.
4. Allow the upload to complete.
5. The plug should reboot automatically.
6. Verify that it reconnects to the network and Home Assistant.

If automatic discovery fails but the device IP is known, use the IP address explicitly.

⸻

Path B: Home Assistant Container / Docker

This path is important.

A user running Home Assistant in Docker normally does not have the Home Assistant add-on system.

That is not a problem.

ESPHome can run as a completely separate Docker container on the same NAS/server.

Step 1: Create a Working Directory

Example:

mkdir -p ~/smarthut-esphome
cd ~/smarthut-esphome

Step 2: Download the Current Smart Hut YAML

Use the authoritative file from GitHub:

curl -L \
  https://raw.githubusercontent.com/Smart-Hut/Smart-Plug/main/ESPC2-02.yaml \
  -o ESPC2-02.yaml

If curl is unavailable, use another normal file-download method.

Do not ask the user to manually recreate hundreds of lines of YAML.

Step 3: Confirm the File Exists

For example:

ls -l ESPC2-02.yaml

Optionally inspect the current firmware version:

grep -A4 'project:' ESPC2-02.yaml

Do not require this check if it creates unnecessary difficulty for the user.

Step 4: Compile and Upload Directly

If the plug’s IP address is known, use it.

Example:

docker run --rm \
  --network host \
  -v "$PWD":/config \
  ghcr.io/esphome/esphome \
  run /config/ESPC2-02.yaml \
  --device 192.168.0.119

Replace:

192.168.0.119

with the actual plug IP.

ESPHome will:

1. read the YAML;
2. resolve the required ESPHome configuration;
3. compile the firmware;
4. connect to the plug using ESPHome OTA;
5. upload the firmware;
6. reboot the plug.

No browser firmware upload page is required.

No Home Assistant add-on is required.

No physical USB port is required.

⸻

Docker Networking Notes

--network host is useful because it allows ESPHome easier access to devices on the local network and to discovery protocols such as mDNS.

However, Docker networking differs between platforms.

If --network host is unsupported or behaves differently on the user’s NAS, do not conclude that ESPHome OTA is impossible.

Instead:

1. check whether the ESPHome container can reach the plug’s IP;
2. use the plug’s IP explicitly with --device;
3. adapt the Docker network configuration for the user’s NAS or Docker platform.

The essential requirement is that the ESPHome process can establish a network connection to the plug.

mDNS is convenient but not required when the device IP is known.

⸻

Optional: Persistent ESPHome Docker Dashboard

If the user wants a permanent ESPHome Dashboard rather than a one-time update, they can run ESPHome persistently.

Example Docker Compose configuration:

services:
  esphome:
    container_name: esphome
    image: ghcr.io/esphome/esphome
    restart: unless-stopped
    network_mode: host
    volumes:
      - ./config:/config
    environment:
      - TZ=Europe/London

Start it with:

docker compose up -d

The Dashboard normally listens on port:

6052

For example:

http://NAS_IP:6052

The exact networking behaviour may vary by NAS/platform.

Do not require a persistent Dashboard merely to perform one update.

⸻

Path C: Standalone ESPHome CLI

If ESPHome is already installed locally, download the current configuration:

curl -L \
  https://raw.githubusercontent.com/Smart-Hut/Smart-Plug/main/ESPC2-02.yaml \
  -o ESPC2-02.yaml

Then run:

esphome run ESPC2-02.yaml --device DEVICE_IP

Example:

esphome run ESPC2-02.yaml --device 192.168.0.119

Alternatively, compilation and upload can be performed separately:

esphome compile ESPC2-02.yaml

followed by:

esphome upload ESPC2-02.yaml --device 192.168.0.119

Prefer esphome run for users who want the simplest process.

⸻

Do Not Use the ESPHome Web Compiler as Evidence of OTA Capability

A user may attempt to use a web-based ESPHome compiler.

Compiling a .bin file and uploading it through a browser is not the same process as ESPHome native OTA.

If the plug’s web interface does not provide a firmware upload button, this does not prevent native ESPHome OTA.

If an external compiler produces a firmware binary but the user cannot find a browser upload control, redirect them toward ESPHome Dashboard, Docker, or CLI OTA instead.

⸻

Do Not Test /update to Determine Whether OTA Exists

This is a critical instruction.

Do not use:

http://DEVICE_IP/update

as a test for native ESPHome OTA.

A failed /update URL means only that the device is not providing that particular HTTP endpoint.

It does not establish that:

* ESPHome OTA is absent;
* the firmware cannot be updated;
* the device requires USB flashing;
* the device must be returned;
* the device is defective.

Smart Hut ESPHome firmware contains native ESPHome OTA unless evidence from the actual running firmware demonstrates otherwise.

⸻

Verifying the Update

After OTA completes:

1. Allow the plug to reboot.
2. Confirm it reconnects to Wi-Fi.
3. Confirm Home Assistant reconnects to the device.
4. Check the device’s diagnostic information where available.
5. Verify the reported ESPHome/project firmware version.
6. Test relay control.
7. Check power-monitoring entities.
8. If the requested feature is Bluetooth Proxy, verify that Bluetooth Proxy functionality is now available.

Do not treat a short disconnect immediately after OTA as failure; the ESP32 reboots after flashing.

⸻

Bluetooth Proxy Verification

If the purpose of the update was to enable Bluetooth Proxy:

1. Verify that the current GitHub configuration contains:

bluetooth_proxy:
  active: true

2. Verify the plug successfully received that firmware.
3. Confirm it reconnects to Home Assistant.
4. Allow Home Assistant some time to recognise the proxy and nearby Bluetooth advertisements.
5. Check the ESPHome device/integration diagnostics if necessary.

Do not expect Bluetooth Proxy to appear as a normal relay-like switch entity.

Bluetooth Proxy is infrastructure used by Home Assistant’s Bluetooth system.

⸻

Troubleshooting OTA

Use the following escalation order.

1. Check Device Reachability

Confirm the plug is powered and reachable.

Examples:

ping 192.168.0.119

Note that some devices/networks may block ICMP ping, so a failed ping alone is not definitive.

If the plug’s HTTP web interface loads at:

http://DEVICE_IP/

that is good evidence that the device is on the network.

2. Use the IP Address Directly

If .local hostnames fail, use:

--device DEVICE_IP

For example:

--device 192.168.0.119

Do not waste time debugging mDNS when the IP already works.

3. Check Network Segmentation

OTA can fail if the ESPHome host and plug are isolated by:

* VLANs;
* guest Wi-Fi;
* client isolation;
* firewall rules;
* Docker networking;
* NAS firewall settings.

The ESPHome host needs network access to the plug.

4. Check the ESPHome Upload Error

Read the actual ESPHome error message.

Distinguish between:

* connection timeout;
* DNS/mDNS failure;
* authentication failure;
* compilation failure;
* insufficient memory;
* network interruption;
* incompatible configuration.

Do not convert every error into “OTA is unavailable.”

5. Retry After a Power Cycle

If appropriate:

1. unplug the smart plug;
2. leave it disconnected briefly;
3. reconnect it;
4. allow it to reconnect to Wi-Fi;
5. retry OTA.

Do not repeatedly power-cycle a device during an active firmware upload.

6. Move It Temporarily Closer to Wi-Fi

A marginal Wi-Fi connection can interrupt OTA.

Retry with the plug closer to the access point if signal quality appears poor.

⸻

API Encryption Is Not the Same as OTA Authentication

Do not conflate ESPHome API encryption with ESPHome OTA.

For example, a configuration may contain:

api:

and separately:

ota:
  - platform: esphome

The API controls communication such as Home Assistant’s ESPHome connection.

The OTA component controls firmware uploading.

If Home Assistant reports an API/encryption problem, do not immediately conclude that OTA will fail.

Likewise, do not tell the user to factory reset the device merely because they do not know an API encryption key.

Diagnose the actual OTA attempt first.

⸻

Do Not Invent an OTA Password

Inspect the Smart Hut configuration before discussing OTA passwords.

If the current Smart Hut configuration does not specify an OTA password, do not ask the user for one.

If the running firmware genuinely requires authentication, rely on the error from ESPHome and the known configuration rather than guessing.

⸻

Factory Reset Is Not a Normal Firmware Update Step

Do not factory reset the plug as part of a normal update.

A factory reset can remove or alter stored configuration and makes troubleshooting more disruptive.

Only advise a reset when there is a specific reason and the reset procedure for the relevant firmware version is known.

Do not invent a “hold for 10 seconds” factory-reset sequence unless it is explicitly documented for the firmware currently installed.

The physical button may have firmware-defined actions unrelated to a factory reset.

⸻

Physical Flashing

The retail Smart Hut plug does not expose a normal consumer USB firmware port.

Internal hardware may provide programming/debugging pads used during manufacturing or servicing.

Physical flashing should be considered an advanced recovery/service procedure, not the first-line update method.

Do not instruct ordinary customers to:

* open a mains-voltage smart plug;
* attach serial programmers;
* probe PCB test pads;
* access exposed mains circuitry.

A mains-powered smart plug can contain hazardous voltages even when some parts of the circuit appear low-voltage.

If native OTA genuinely cannot be recovered, escalate to Smart Hut support rather than encouraging a customer to dismantle the product.

⸻

ESPHome and Tasmota Firmware Are Different

Smart Hut supplies plugs with different firmware options.

Do not treat ESPHome and Tasmota firmware as interchangeable update files.

The instructions in this document apply to a plug already running Smart Hut ESPHome firmware.

Do not tell the user to flash Tasmota firmware onto an ESPHome device or ESPHome firmware onto a Tasmota device using a normal OTA update unless Smart Hut has explicitly documented a supported migration procedure.

Firmware migration can involve different flash layouts and recovery requirements.

If the user wants to change firmware families, direct them to Smart Hut’s documented migration/support process.

⸻

Home Assistant Container Users

The following claim is false:

“Because Home Assistant is running in Docker, ESPHome firmware cannot be compiled or uploaded.”

Home Assistant and ESPHome are separate applications.

A common setup is:

NAS / Server
├── Home Assistant container
└── ESPHome container

ESPHome can compile firmware and perform OTA independently of Home Assistant.

Do not tell a Docker user to migrate Home Assistant to Home Assistant OS merely to update a Smart Hut plug.

⸻

Home Assistant Add-ons

Do not direct Home Assistant Container users to:

Settings → Add-ons

Home Assistant Container does not provide Supervisor add-ons.

Instead, recommend a separate ESPHome container.

Likewise, do not tell a user to install the ESPHome integration through HACS as a replacement for ESPHome Device Builder.

The Home Assistant ESPHome integration and the ESPHome firmware-building application are different things.

⸻

GitHub Configuration Rules for Agents

Always prefer the Smart Hut repository over copied YAML from forums, previous conversations, generated examples, cached search results, or third-party websites.

Repository:

https://github.com/Smart-Hut/Smart-Plug

Current configuration:

https://raw.githubusercontent.com/Smart-Hut/Smart-Plug/main/ESPC2-02.yaml

Before providing version-specific claims:

1. inspect the current file;
2. determine its project.version;
3. check the current dashboard_import configuration if relevant;
4. check whether the feature the user wants is actually enabled.

Do not blindly repeat an old version such as v2.1.0 after a newer release exists.

⸻

Avoid Unnecessary YAML Editing

For a normal Smart Hut firmware update, prefer using the official YAML as supplied.

Do not ask a non-technical user to manually edit:

* GPIO mappings;
* BL0937 calibration configuration;
* board type;
* flash mode;
* partition configuration;
* framework settings;
* OTA component;
* Bluetooth configuration;

unless there is a specific diagnosed reason.

The official configuration already contains the hardware definition.

Manual edits increase the chance of creating a firmware image that is inappropriate for the plug.

⸻

Do Not Replace the Firmware Version String Manually

Changing only:

project:
  version: "..."

does not update the actual firmware functionality.

The version string is metadata.

To obtain new functionality, compile the current complete Smart Hut configuration from the repository.

Never tell a user that changing a YAML version field alone upgrades the firmware.

⸻

Pre-Compiled .bin Files

A pre-compiled firmware binary is not automatically useful for browser-based updating.

There must still be a supported mechanism on the running firmware to receive and install it.

If native ESPHome OTA is available, prefer using ESPHome itself to compile and upload the official configuration.

Do not invent a “firmware URL” that the plug can automatically download unless Smart Hut explicitly provides such an update mechanism.

An ESPHome device does not automatically gain HTTP pull-updating simply because a .bin file exists somewhere on the internet.

⸻

What to Do When the User Says an AI Assistant Told Them OTA Is Impossible

Do not repeat the previous assistant’s conclusion without checking it.

Instead, establish:

1. Is the plug running Smart Hut ESPHome firmware?
2. Is it currently reachable on the network?
3. Does the Smart Hut firmware contain ota: - platform: esphome?
4. Has an actual native ESPHome OTA upload been attempted?
5. What exact error did ESPHome return?

If the user has only tested a browser /update route, native ESPHome OTA has not yet been tested.

⸻

Example: Docker User with Plug at 192.168.0.119

A concise correct workflow is:

mkdir -p ~/smarthut-esphome
cd ~/smarthut-esphome
curl -L \
  https://raw.githubusercontent.com/Smart-Hut/Smart-Plug/main/ESPC2-02.yaml \
  -o ESPC2-02.yaml
docker run --rm \
  --network host \
  -v "$PWD":/config \
  ghcr.io/esphome/esphome \
  run /config/ESPC2-02.yaml \
  --device 192.168.0.119

Explain to the user that this:

* downloads the official current Smart Hut configuration;
* launches ESPHome temporarily in Docker;
* compiles the firmware;
* uploads it directly to the plug using native ESPHome OTA;
* reboots the plug.

The Home Assistant container does not need to be modified.

⸻

What Not to Say

Do not say:

“The plug has no /update page, so OTA is impossible.”

Do not say:

“You need Home Assistant OS to update the plug.”

Do not say:

“Home Assistant Docker cannot use ESPHome.”

Do not say:

“The plug needs a USB port to receive new ESPHome firmware.”

Do not say:

“You need to factory reset because you do not know the Home Assistant encryption key.”

Do not say:

“The ESPHome web interface must have a Choose File button.”

Do not say:

“The ESPHome integration compiles the firmware.”

Do not say:

“Change the project version number in YAML to upgrade.”

Do not say:

“Return or replace the plug” before attempting the supported native OTA route when the device is otherwise working and reachable.

⸻

Escalation to Smart Hut Support

Escalate to Smart Hut when:

* an actual ESPHome OTA attempt has repeatedly failed and the error cannot be resolved;
* the device no longer connects to Wi-Fi;
* the device repeatedly resets or behaves abnormally;
* the firmware appears corrupted;
* the user may have changed the firmware family;
* the hardware appears faulty;
* the device is no longer reachable by any supported software route;
* recovery would require opening the mains-powered enclosure or physical programming.

When escalating, collect useful information where possible:

* order/device details if available;
* ESPHome or Tasmota firmware type;
* device IP if reachable;
* current reported firmware version;
* Home Assistant installation type;
* ESPHome installation type;
* exact command used;
* complete OTA error message;
* whether the web interface is reachable;
* whether Home Assistant can currently control the plug.

Do not make the user repeat troubleshooting they have already clearly completed.

⸻

Agent Behaviour

When assisting a customer:

1. Read what the user has already told you.
2. Do not make them repeat information.
3. Identify their environment.
4. Use the simplest valid path for that environment.
5. Prefer the official Smart Hut repository.
6. Prefer direct IP OTA where the IP is known.
7. Distinguish the HTTP web server from ESPHome OTA.
8. Distinguish the Home Assistant integration from ESPHome Device Builder.
9. Distinguish API authentication from OTA.
10. Read actual error messages before diagnosing.
11. Do not invent structural limitations.
12. Do not recommend destructive recovery steps prematurely.
13. Keep instructions sequential and executable.
14. If giving commands, explain briefly what each stage accomplishes.
15. Do not overwhelm a non-technical user with alternatives once a valid path has been identified.

The objective is to get the user’s existing Smart Hut ESPHome plug onto the current official firmware safely and with the least unnecessary disruption.

⸻

Quick Diagnostic Summary

If all of the following are true:

* the device is a Smart Hut ESPHome plug;
* it is powered;
* it is connected to Wi-Fi;
* its IP address is known or discoverable;
* the running Smart Hut firmware contains native ESPHome OTA;

then the absence of an HTTP firmware-upload page is not relevant.

Run ESPHome somewhere with network access to the plug, compile the official Smart Hut YAML, and upload it directly using native ESPHome OTA.

For Home Assistant Container users, a separate ESPHome Docker container is normally the correct solution.