---
title: Nest
description: Instructions on how to integrate Nest into Home Assistant.
ha_category:
  - Hub
  - Binary Sensor
  - Camera
  - Climate
  - Sensor
ha_iot_class: Cloud Push
ha_release: 0.7
ha_config_flow: true
ha_codeowners:
  - '@awarecan'
  - '@allenporter'
ha_domain: nest
---

The `nest` integration allows you to integrate your [Google Nest](https://store.google.com/us/category/connected_home?) devices in Home Assistant. This integration uses the [Smart Device Management](https://developers.google.com/nest/device-access/api) API and Google's Cloud Pubsub to efficiently listen for changes in device state or other events.

There is currently support for the following device types within Home Assistant:

- [Camera](#camera)
- [Climate](#climate)
- [Sensor](#sensor)

<div class='note'>
Note that this integration continues to support the Legacy Works With Nest API which is not accepting new users. The documentation for this API is at the bottom of this page so existing users can keep using it.
</div>

## Overview: Supported Devices

Home Assistant is integrated with the following devices through the SDM API:

- Thermostat Devices
  - Every thermostat is exposed as a `climate` entity
  - Temperature and Humidity sensors each have a `sensor` entity
  - Example devices: All Google Nest Thermostat models
- Display, Camera, and Doorbell Devices
  - The camera live stream is available as a `camera` entity
  - Example devices: All Google Nest Cam models, Google Nest Hello Video Doorbell, Google Nest Hub Max

You are in control of the information and capabilities exposed to Home Assistant. You can authorize a single device, multiple devices, or different levels of functionality such as motion events, live streams, for any particular device. The integration is flexible enough to adapt based on what you allow.

Others devices like Smoke and CO Alarms or Security systems are not currently
supported by the SDM API.

## Account Setup

You will need to follow the instructions in [Device Access Registration](https://developers.google.com/nest/device-access/registration), which includes the following steps in the
Quick Start Guide:

- Accept the Terms of Service.
- Pay a fee (currently US$5).
- Register in the Device Access Console to get a `project_id`.
- Authorize your Google Account and create OAuth credentials to get a `client_id` and `client_secret`.
- Enable pubsub events in the Device Access Console (creates a topic).
- Create a pull subscription to get a `subscriber_id` ("Subscription ID" in Google Cloud Console).

<div class='note warning'>
It is currently not possible to share/be invited to a home with a G-Suite account. Make sure that you pay the fee with an account that has access to your devices.
</div>

Additionally, Home Assistant must be configured with a URL (e.g., external exposed [`http`](/integrations/http/), Nabu Casa, etc). When setting up the OAuth credentials, make sure the Home Assistant URL is in the list of *Authorized redirect URIs*, so the redirect back to Home Assistant can get an OAuth authorization code.

Follow all of the instructions in [Device Access: Quick Start Guide](https://developers.google.com/nest/device-access/get-started) carefully as it is easy to make a configuration mistake that is difficult to debug. It is recommended to exercise the entire guide, including the command to test out the API, to make sure that it is working before configuring Home Assistant.

It may be easiest to create a [Pub/Sub subscription](https://console.cloud.google.com/cloudpubsub/subscription/list) from the Google Cloud console. Make sure to use the *topic name* from the device access console and a unique subscription ID. Note the message retention is how long messages will queue while offline, so keep that short (e.g., under an hour) to avoid a potentially large backlog of updates.

## Configuration

```yaml

```yaml
# Example configuration.yaml entry
nest:
  client_id: CLIENT_ID
  client_secret: CLIENT_SECRET
  project_id: PROJECT_ID    # ("Project ID" in the Device Access Console)
  subscriber_id: SUBSCRIBER_ID # ("Subscription ID" in Google Cloud Console)
```

{% configuration %}
client_id:
  description: Your Device Access or Nest developer client ID.
  required: true
  type: string
client_secret:
  description: Your Device Access or Nest developer client secret.
  required: true
  type: string
project_id:
  description: Your Device Access Project ID. This enables the SDM API.
  required: false
  type: string
subscriber_id:
  description: Your Pub/sub Subscription ID used to receive events. This is required to use the SDM API.
  type: string
  required: false
{% endconfiguration %}

## Device Setup

Once your developer account is set up and you have a valid `nest` entry in `configuration.yaml` , you need to connect devices with the following steps:

1. From the Home Assistant front-end, navigate to **Configuration** then **Integrations**. Under **Set up a new integration** locate 'Nest'.
1. You should get redirected to Google to choose an account. This should be the same developer account you configured above.
1. The *Google Nest permissions* screen will allow you to choose which devices to configure. You can leave out any device that you do not wish to use with Home Assistant.
1. You will get redirected back to another account selection page.
1. Confirm you want to allow persistent access to Home Assistant.

## Troubleshooting

- See [No URL Available](/more-info/no-url-available) for guidance on setup issues related to URLs.

- For trouble with the SDM API OAuth authorization flow with Google, see [Troubleshooting](https://developers.google.com/nest/device-access/authorize#troubleshooting) which includes guidance for errors like `redirect_uri_mismatch` where Google needs to know about your external URL

## Camera

All Google Nest Cam models, Google Nest Hello Video Doorbell, Google Nest Hub Max expose a [CameraLiveStream](https://developers.google.com/nest/device-access/traits/device/camera-live-stream) via the SDM API, which returns a RTSP live stream which can be viewed from Home Assistant.

Given a camera named `Front Yard` then the camera is created with a name such as `camera.front_yard`.

## Climate

All Google Nest Thermostat models are exposed as a `climate` entity that use the [Thermostat Traits](https://developers.google.com/nest/device-access/traits/device/thermostat-hvac) in the SDM API. State changes to the thermostat are reported to Home Assistant through the Cloud Pubsub subscriber.

Given a thermostat named `Upstairs` then the climate entity is created with a name such as `climate.upstairs`

## Sensor

All Google Nest Thermostat models have traits exposed from the SDM API. The initial values of the sensors are fetched on startup, then updated regularly using the Cloud Pubsub subscriber. The following traits are supported with sensors:

- [Temperature](https://developers.google.com/nest/device-access/traits/device/temperature)
- [Humidity](https://developers.google.com/nest/device-access/traits/device/humidity)

Given a thermostat named `Upstairs` then sensors are created with names such as `sensor.upstairs_temperature` or `sensor.upstairs_humidity`.

# Legacy Works With Nest API

This section contains instructions for the Legacy [Works with Nest](https://developers.nest.com/) API. 

<div class='note warning'>
New users are not currently able to set up a Works With Nest Developer account. The documentation is preserved here for existing users of the API.
</div>

<details>
<summary>Click here for documentation for the Legacy Works with Nest API</summary>

The Nest integration is the main integration to integrate all [Nest](https://nest.com/) related platforms. To connect Nest, you will have to [sign up for a developer account](https://developers.nest.com/products) and get a `client_id` and `client_secret`.


There is currently support for the following device types within Home Assistant:

- [Binary Sensor](#binary-sensor)
- [Camera](#camera)
- [Climate](#climate)
- [Sensor](#sensor)

### Setting up developer account


1. Visit [Nest Developers](https://developers.nest.com/), and sign in. Create an account if you don't have one already.
2. Fill in account details:
  * The "Company Information" can be anything. We recommend using your name.
3. Submit changes
4. Click "[Products](https://developers.nest.com/products)" at top of page.
5. Click "[Create New Product](https://developers.nest.com/products/new)"
6. Fill in details:
  * Product name must be unique. We recommend [email] - Home Assistant.
  * The description, users, URLs can all be anything you want.
  * Leave the "Redirect URI" Field blank
7. For permissions check every box and if it's an option select the read/write option. Note: there are important permissions under the "Other Permissions" category. If you are only adding a thermostat, do not just select the permissions under "Thermostat". You still need to check the boxes under "Other Permissions" in order to give you access to features like away mode, ETA, structure read/write, and postal code.
  * The description requires a specific format to be accepted.
    * Use "[Home Assistant] [Edit] [For Home Automation]" as the description as it is not super important.
8. Click "Create Product"
9. Once the new product page opens the "Product ID" and "Product Secret" are located on the right side. These will be used as `client_id` and `client_secret` below.
10. Add the Nest integration to your `configuration.yaml` and restart Home Assistant. Then, go to `Configuration > Integrations` and select `CONFIGURE` next to `Nest`. Click the link in the configurator pop up to log into your Nest account and complete the OAuth. Copy the resulting PIN code into the pop up.

Connecting to the Nest Developer API requires outbound port 9553 on your firewall. The configuration will fail if this is not accessible.

## Configuration

```yaml
# Example configuration.yaml entry
nest:
  client_id: CLIENT_ID
  client_secret: CLIENT_SECRET
```

```yaml
# Example configuration.yaml entry to show only devices at your vacation and primary homes
nest:
  client_id: CLIENT_ID
  client_secret: CLIENT_SECRET
  structure:
    - Vacation
    - Primary
```

{% configuration %}
client_id:
  description: Your Nest developer client ID.
  required: true
  type: string
client_secret:
  description: Your Nest developer client secret.
  required: true
  type: string
structure:
  description: The structure or structures you would like to include devices from. If not specified, this will include all structures in your Nest account.
  required: false
  type: list
{% endconfiguration %}

### Service `set_away_mode`

You can use the service `nest/set_away_mode` to set the structure(s) to "Home" or "Away".

| Service data attribute | Optional | Description |
| ---------------------- | -------- | ----------- |
| `away_mode` | no | String, must be `away` or `home`.
| `structure` | yes | String, will default to all configured Nest structures if not specified.

Examples:

```yaml
# Example script to set away, no structure specified so will execute for all
script:
  nest_set_away:
    sequence:
      - service: nest.set_away_mode
        data:
          away_mode: away
```

```yaml
# Example script to set home, structure specified
script:
  nest_set_home:
    sequence:
      - service: nest.set_away_mode
        data:
          away_mode: home
          structure:
            - Apartment
```

### Service `set_eta`

You can use the service `nest/set_eta` to set or update the estimated time of arrival window. Calling this service will automatically set the structure(s) to "Away". Structures must have an associated Nest thermostat in order to use ETA function.

| Service data attribute | Optional | Description |
| ---------------------- | -------- | ----------- |
| `eta` | no | Time period, estimated time of arrival from now.
| `eta_window` | yes | Time period, estimated time of arrival window. Default is 1 minute.
| `trip_id` | yes | String, unique ID for the trip. Default is auto-generated using a timestamp. Using an existing `trip_id` will update that trip's ETA.
| `structure` | yes | String, will default to all configured Nest structures if not specified.

Examples:

```yaml
# Example script to set ETA, no structure specified so will execute for all
script:
  nest_set_eta:
    sequence:
      - service: nest.set_eta
        data:
          eta: 00:10:30
          trip_id: Leave Work
```

```yaml
# Example script to update ETA and specify window, structure specified
script:
  nest_update_eta:
    sequence:
      - service: nest.set_eta
        data:
          eta: 00:11:00
          eta_window: 00:05
          trip_id: Leave Work
          structure:
            - Apartment
```

### Service `cancel_eta`

You can use the service `nest/cancel_eta` to cancel an existing estimated time of arrival window. Structures must have an associated Nest thermostat in order to use ETA function.

| Service data attribute | Optional | Description |
| ---------------------- | -------- | ----------- |
| `trip_id` | no | String, unique ID for the trip. Using an existing `trip_id` will update that trip's ETA.
| `structure` | yes | String, will default to all configured Nest structures if not specified.

Examples:

```yaml
# Example script to cancel ETA, no structure specified so will execute for all
script:
  nest_cancel_eta:
    sequence:
      - service: nest.cancel_eta
        data:
          trip_id: Leave Work
```

```yaml
# Example script to cancel ETA, structure specified
script:
  nest_cancel_eta:
    sequence:
      - service: nest.cancel_eta
        data:
          trip_id: Leave Work
          structure:
            - Apartment
```

### Troubleshooting

- For trouble with the SDM API OAuth authorization flow with Google, see [Troubleshooting](https://developers.google.com/nest/device-access/authorize#troubleshooting) which includes guidance for errors like `redirect_uri_mismatch` where Google needs to know about your external URL.

- If you're getting [rickrolled](https://www.youtube.com/watch?v=dQw4w9WgXcQ) by the Legacy API instead of being able to see your Nest cameras, you may not have set up your developer account's permissions correctly. Go back through and make sure you've selected read/write under every category that it's an option.

## Platforms

<div class='note'>

You must have the [Nest component](/integrations/nest/) configured to use the platforms below.

</div>

### Binary Sensor

The `nest` binary sensor platform lets you monitor various states of your [Nest](https://nest.com) devices.

<div class='note'>

You must have the [Nest component](/integrations/nest/) configured to use these sensors. The binary sensors will be setup if the `nest` integration is configured and the required configuration for the `nest binary sensor` is set.

</div>

#### Configuration

To enable binary sensors and customize which sensors are setup, you can extend the [Nest component](/integrations/nest/) configuration in your `configuration.yaml` file with the following settings:

```yaml
# Example configuration.yaml entry
nest:
  binary_sensors:
    monitored_conditions:
      - 'fan'
      - 'target'
```

By default all binary sensors for your available Nest devices will be monitored. Leave `monitored_conditions` blank to disable all binary sensors for the [Nest component](/integrations/nest/).

{% configuration %}
monitored_conditions:
  description: States to monitor.
  required: false
  type: list
{% endconfiguration %}

The following conditions are available by device:

- Nest Home:
  - away
- Nest Thermostat:
  - online
  - fan
  - is\_using\_emergency\_heat
  - is\_locked
  - has\_leaf
- Nest Protect:
  - online
- Nest Camera:
  - online
  - motion\_detected
  - person\_detected
  - sound\_detected

### Camera

The `nest` platform allows you to watch still frames from a video stream (not live stream) of your [Nest](https://nest.com/camera/meet-nest-cam/) camera in Home Assistant.

<div class='note'>

The Legacy API integration allows you to watch still frames from a video stream (not live stream). The Legacy API also supports the `camera.turn_on` and `camera.turn_off` services.

</div>

Nest Camera supports the `camera.turn_on` and `camera.turn_off` services since the 0.75 release.

### Climate

The `nest` climate platform lets you control a thermostat from [Nest](https://nest.com).

<div class='note'>
Please note due to limitations with the European Nest Thermostat E, integration with Home Assistant for that thermostat is not possible.
</div>

<p class='img'>
  <img src='/images/screenshots/nest-thermostat-card.png' />
</p>

### Sensor

The `nest` sensor platform lets you monitor sensors connected to your [Nest](https://nest.com) devices.

<div class='note'>

The sensors will be setup if the `nest` integration is configured and the required configuration for the `nest sensor` is set.

</div>

#### Configuration

To enable sensors and customize which sensors are setup, you can extend the [Nest component](/integrations/nest/) configuration in your `configuration.yaml` file with the following settings:

```yaml
# Example configuration.yaml entry
nest:
  sensors:
    monitored_conditions:
      - 'temperature'
      - 'target'
```

By default all sensors for your available Nest devices will be monitored. Leave `monitored_conditions` blank to disable all sensors for the [Nest component](/integrations/nest/).

{% configuration %}
monitored_conditions:
  description: States to monitor.
  required: false
  type: list
{% endconfiguration %}

The following conditions are available by device:

- Nest Home:
  - `eta`: Estimated time of arrival.
  - `security_state`: `ok` or `deter`. [Security State](#security-state). Only available when Nest Camera exists.
- Nest Thermostat:
  - `humidity`
  - `preset_mode`
  - `temperature`
  - `target`
  - `hvac_state`: The currently active state of the HVAC system, `heat`, `cool` or `off` (previously `heating`, `cooling` or `off`).
- Nest Protect:
  - `co_status`: `Ok`, `Warning` or `Emergency`
  - `smoke_status`: `Ok`, `Warning` or `Emergency`
  - `battery_health`: `Ok` or `Replace`
  - `color_status`: `gray`, `green`, `yellow` or `red`. Indicates device status by color in the Nest app UI. It is an aggregate condition for battery+smoke+CO states, and reflects the actual color indicators displayed in the Nest app.
- Nest Camera: none

### Security State

<div class='note warning'>

This feature is not designed to transform your Home Assistant into a security system, neither Home Assistant nor Nest are liable for damages,
or consequential damages of any character arising as a result of use this feature.

This feature does not depend on the [Nest Secure alarm system](https://nest.com/alarm-system/overview/) and is not a reflection of the status of that system,
nor does it react to state changes in that system.

</div>

<div class='note'>

This feature uses a new [Nest Security API](https://developers.nest.com/documentation/cloud/security-guide).
You may need to change your ["Product"](https://developers.nest.com/products) permission setting to include `Security State Read`.
After this permission change, you may need to re-authorize your client.

</div>

If a Nest Cam detects the presence of a person (see `person_detected` in [binary_sensor.nest](#binary-sensor) while the structure is in `away` mode (see `away` in [binary_sensor.nest](#binary-sensor), the structure enters `deter` mode.

A `deter` state is re-evaluated after several minutes and relaxed to `ok` if no further `person_detected` events have occurred.

The `security_state` automatically switches to `ok` when the structure state is `home`.

</details>
