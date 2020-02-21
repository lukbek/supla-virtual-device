# SUPLA VIRTUAL-DEVICE

![supla-virtual-device](https://github.com/lukbek/supla-core/workflows/supla-virtual-device/badge.svg?branch=supla-mqtt-dev)

This is a fork of [`supla-dev`](https://github.com/SUPLA/supla-core/tree/master/supla-dev) and evolution of [`supla-filesensors`](https://github.com/fracz/supla-filesensors) that is able to read measurement values from files or MQTT and send them to the SUPLA, so you can display them in the app, create direct links etc. You can create buttons and perform actions such as publishing messages to MQTT or executing system commands via the SUPLA application.

<b>Your are using this software for your own risk. Please don't rely on it if it comes to a life danger situation.</b>


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
[CHANNEL_X]
function=TEMPERATURE
state_topic=sensors/temp/kitchen/state
payload_value=/data/temp
```

* `payload_value`: [`JSONPointer`](https://tools.ietf.org/html/rfc6901) to the value in JSON payload 
   example above assumes that payload will look like {"data": { "temp": 23.5 } }

# Executing system command
```
[CHANNEL_X]
function=GATEWAYLOCK
command=echo 'Hello World' >> helloworld.txt
```
* `command`: system command that will be executed on switch value changed
   example above is working with monostable button 
```
[CHANNEL_X]
function=POWERSWITCH
command_on=echo 'Power On!' >> power.txt
command_off=echo 'Power Off!' >> power.txt
```
* `command_on`: system command that will be executed when channel value change to 1
* `command_off`: system command that will be executed when channel value change to 0

# Publishing MQTT device command
```
[CHANNEL_X]
function=POWERSWITCH
state_topic=switch/kitchen/state
payload_on=1
payload_off=0
command_topic=switch/kitchen/command
command_template=$value$
```
* `command_topic`: MQTT publish topic
* `command_template`: MQTT payload $value$ will be replaced with channel current value
* `payload_on`: value template that means channel on value
* `payload_off`: value template that means channel off value

# Support 

Feel free to ask on [`SUPLA's forum`](https://forum.supla.org/viewtopic.php?f=9&t=6189) for this software and report issues on github.












