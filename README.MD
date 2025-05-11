# Warema WMS Add-on for Home Assistant

Just another fork of the addon-warena-bridge. I did this to add 'Actuator 230 V UP' as a device for my particula setup at home. Multiple Handheld single channel recivers and the 'new' actuator.

This Home Assistant add-on allows users to control Warema WMS (Wireless Mains Supply) equipment, such as blinds, awnings, and other supported devices, directly from Home Assistant. To use this add-on, you will need a [Warema WMS stick](https://www.warema.com/en/control-systems/radio-systems/supplementary-components/).

## About This Fork

This repository is a fork of another fork of the original Warema Bridge add-on maintained by [giannello](https://github.com/giannello/addon-warema-bridge). So this fork is the 3rd fork.
1) [giannello](https://github.com/giannello/addon-warema-bridge)
2) [dbffm](https://github.com/dbffm/addon-warema-bridge)
3) This fork by me...

The original add-on is an excellent solution for integrating Warema devices with Home Assistant;  The second solved the issues when connecting to an external MQTT server that required adjustments to the configuration. This fork updated the underlying java script library https://www.npmjs.com/package/warema-wms-venetian-blinds?activeTab=readme from version 1.1.18 to 2.0.5 and add the functionality for the relative new device type '2E' aka 'Actuator 230 V UP' from Warema.

### Issue with MQTT Server in the Original Repository - 2. Fork

In the original version, the add-on was set up to connect only with the default MQTT broker (`core-mosquitto`) within Home Assistant. This configuration did not support connecting to an external or custom MQTT server. When users attempted to specify their MQTT settings (such as server URL, username, and password) directly in YAML, these settings were not passed correctly as environment variables to the add-on's runtime environment. As a result, the add-on continued searching for the `core-mosquitto` server even when a custom server was specified. This caused the add-on to fail whenever `core-mosquitto` was not set up or intended to be used.

### Root Cause and Solution - 2. Fork

The issue stemmed from the `config.json` file, where the schema did not define fields for MQTT server settings such as `mqtt.server`, `mqtt.user`, or `mqtt.password`. Without these fields, the add-on’s UI did not expose options for setting the external MQTT server, making it impossible for the user to input and pass the necessary configuration values.

To fix this, the `config.json` file was updated to include the missing MQTT configuration fields:

- **`mqtt.server`**: The URL of the MQTT server (e.g., `http://localhost:1883`).
- **`mqtt.user`**: Username for MQTT server authentication.
- **`mqtt.password`**: Password for MQTT server authentication.

Additionally, the `run.sh` script was modified to correctly read and pass these environment variables to the underlying application, ensuring that the external MQTT server settings are used properly.

These changes allow the user to configure the add-on with an external MQTT server from the Home Assistant UI, solving the problem of incorrect server detection.

## Enhancements in 2. Fork

1. **Added MQTT Configuration to UI**: Modified the `config.json` to include `mqtt.server`, `mqtt.user`, and `mqtt.password` in the schema. This allows users to configure these settings directly from the Home Assistant add-on UI.
2. **Modified Run Script**: Updated the `run.sh` script to read the newly added environment variables and pass them correctly to the add-on's runtime environment, enabling support for external MQTT servers.
3. **Support for Custom MQTT Servers**: The add-on now supports external or custom MQTT servers, not just the internal `core-mosquitto` server, providing more flexibility for different user setups.

### Issue with device type '2E' aka 'Actuator 230 V UP' from Warema - 3. Fork
After installing and configuring the second fork correctly the addon-warema-bridge appear to be 'offline' at the external mqtt server by posting the corresponding telegram message to the warema topic.
At loglevel you would find after the scanning section of your stick the message: '2E' unknown device type
Indicating the missing functionality for the 'Actuator 230 V UP' from Warema.

### Root Cause and Solution - 3. Fork
Looking into the code of the original addon you find the reference to warema-wms-venetian-blinds java script library. This is also referenced by the second fork. Looking up the warema-wms-venetian-blinds library at npm you find, that 

## Installation

1. Install the add-on from the Home Assistant add-on store.
2. Configure the add-on using the parameters listed below.
3. Start the add-on to begin controlling your Warema devices.

## Configuration Options

- **`MQTT_SERVER`**: MQTT server URL (e.g., `mqtt://localhost:1883`).
- **`MQTT_USER`**: Username for MQTT server authentication.
- **`MQTT_PASSWORD`**: Password for MQTT server authentication.
- **`IGNORED_DEVICES`**: A comma-separated list of device IDs to exclude from the results of network scanning.
- **`FORCE_DEVICES`**: A comma-separated list of devices to monitor for status and position updates.
- **`POLLING_INTERVAL`**: Time interval in ms between each request for all devices to report their position and state (Default: `30000`).
- **`MOVING_INTERVAL`**: Time interval between each request to a moving device to report position and state (Default: `1000`, Minimum: `100`).
- **`WMS_CHANNEL`, `WMS_KEY`, `WMS_PAN_ID`**: Parameters to configure the WMS network your devices are connected to.
- **`WMS_SERIAL_PORT`**: Device path for the WMS USB Key (Default: `/dev/ttyUSB0`).
- **`LOG_LEVEL`**: Log level, one of `[error, warn, info, http, verbose, debug, silly]` (Default: `info`).

### WMS Network Parameters Discovery

To control WMS devices, the add-on must be configured with the correct network parameters. These can be discovered by starting the add-on with a `PAN_ID` set to `FFFF` and following the instructions:

1. Start the add-on with `PAN_ID` set to `FFFF`.
2. Follow the instructions displayed in the console to use a WMS Handheld transmitter to discover the network parameters.

Follow the steps displayed in the console to retrieve your network configuration. Once the correct parameters (Channel, PanId, Key) are discovered, restart the add-on with the new configuration.

## Credits and Contribution
This fork is based on the original add-on created by [giannello](https://github.com/giannello/addon-warema-bridge). Additionally, some of the information in this README.md was inspired by a fork from [santam85](https://github.com/santam85/addon-warema-wms), which provided detailed configuration and usage instructions.
