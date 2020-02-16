# SUPLA VIRTUAL-DEVICE

This is a fork of [`supla-dev`](https://github.com/SUPLA/supla-core/tree/master/supla-dev) and evolution of [`supla-filesensors`](https://github.com/fracz/supla-filesensors) that is able to read measurement values from files or MQTT and send them to the SUPLA, so you can display them in the app, create direct links etc. You can create buttons and perform actions such as publishing messages to MQTT or executing system commands via the SUPLA application.

This software can be used to connect hardware with non-SUPLA firmware to SUPLA Cloud as well as supply SUPLA with data from websites and files.

If you want to use it, you can have your SUPLA account on official public `cloud.supla.org` service or private one, 
but you need some machine to run `supla-virtual-device` for you. It may be anything running linux, 
e.g. RaspberryPi or any Raspberry-like creation, VPS, your laptop etc.

# Supported sensors

* `TEMPERATURE` - sends a value from file as a temperature
* `TEMPERATURE_AND_HUMIDITY` - sends two values for a temperature and humidity 
* `DISTANCESENSOR`, `PRESSURESENSOR`, `RAINSENSOR`, `WEIGHTSENSOR`, `WINDSENSOR`, `DEPTHSENSOR` - send a value from file pretending to be the corresponding sensor
* `GATEWAYSENSOR`, `GATESENSOR`, `GARAGE_DOOR_SENSOR`, `NOLIQUID`, `DOORLOCKSENSOR`, `WINDOWSENSOR` - sends a 0/1 value from file to SUPLA 
* **SOMEDAY**: `HUMIDITY` - sends a single value as a humidity (no corresponding hardware, but does not display any unit in the SUPLA app)
* **SOMEDAY**: `GENERAL` - sends a single value to the [general purpose measurement channel type](https://forum.supla.org/viewtopic.php?f=17&t=5225) (to be released in the next upcoming SUPLA release)
* **SOMEDAY**: `IC_ELECTRICITY_METER`, `IC_GAS_METER`, `IC_WATER_METER`

Do not be mistaken that it can send only temperature and humidity values. It can be anything (see examples below).
However, while waiting for the general purpose measurement channel in SUPLA, we must pretend these values are
either temperature or humidity although they can mean completely different thing to you. Setting appropriate icon 
and description should help.

# Control device supported

* `GATEWAYLOCK` `GATE` `GARAGEDOOR`, `DOORLOCK`, `POWERSWITCH`, `LIGHTSWITCH`
* **SOMEDAY**: `DIMMER`, `RGBLIGHTNING`, `DIMMERANDRGB`, `ROLLERSHUTTER`

# Installation

```
sudo apt-get update
sudo apt-get install -y git libssl-dev build-essential curl
git clone https://github.com/lukbek/supla-virtual-device.git
cd supla-virtual-device
./install.sh
```

### Upgrade

Stop `supla-virtual-device` and execute:

```
cd supla-virtual-device
./install.sh
```

# Configuration

There is a `supla-virtual-device.cfg` file created for you after installation.
In the `host`, `ID` and `PASSWORD` fields you should enter valid SUPLA-server
hostname, identifier of a location and its password. If you want to use it with MQTT 
you must fill MQTT section fields like `host`, `port` and if used `username` and `password` 
After successful lauch of the `supla-virtual-device` it will create a device in that location.

Then you can put as many channels in this virtual device as you wish, 
following the template:

# Sensors with data from file
```
[CHANNEL_X]
function=TEMPERATURE
file=/home/pi/supla-filesensors/var/raspberry_sdcard_free.txt
min_interval_sec=300
```

* `CHANNEL_X` should be the next integer, starting from 0, e.g. `CHANNEL_0`, `CHANNEL_1`, ..., `CHANNEL_9`
* `function` should be set to one of the supported values mentioned above (depending on the way of presentation you need)
* `file` should point to an absolute path of the file that will contain the measurements
* `min_interval_sec` is a suggestion for the program of how often it should check for new measurements in the
  file; it is optional with a default value of `10` (seconds); if the measurement does not change often, it's
  good idea to set a bigger value not to stress your sd card with too many reads

# Sensors with data from MQTT (Raw data)
```
[CHANNEL_X]
function=TEMPERATURE
state_topic=sensors/temp/kitchen/state
```
* `state_topic`: the exact topic that will be subscribed to by supla-virtual-device
   raw value means that in payload id should be raw single value like 23.5 (dot separated)

# Sensors with data from MQTT (Json)
```
[


