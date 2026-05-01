<h3 align="right"><a href="https://www.tinkoff.ru/rm/yakovleva.irina203/51ZSr71845" target="_blank">your "thank you" to the author</a></h3>
<h3 align="right"><a href="https://t.me/tombraider2006" target="_blank">author's Telegram channel</a></h3>
<h5 align="right">give the project a "star" so other users can find it more easily.</h5>


## Installing the Filament Motion Sensor Instead of the Standard Filament Runout Sensor

[**english version**](/mans/sfs2_en.md)

![](/images/sfs_1.png)

Given the large print area of this printer, significant print times, and high filament consumption, monitoring only for filament runout is clearly not sufficient. The cost of a filament jam failure would be extremely high, so installing a system that monitors both runout and jams is an excellent solution.

This will minimize the risk of failures and prevent losses of time and materials.

Since a standard filament sensor uses 3 wires while the SFS 2.0 uses 4, there are two installation methods:
* The first involves connecting only the motion sensor in place of the runout sensor, which is logical — if there is no filament, it won't move either, which will still trigger the sensor.
* However, routing an additional wire in our printer is not very difficult, so we will also cover that option.

## Method 1

(replacing the sensor without additional wiring)

For this, we need to rearrange the wires into a 4-pin connector, for example like this:

![](/images/sfs2_connector.jpg)

Connector pin assignments:

![](/images/sfs_pin.png)


Open `printer.cfg` and find the lines:

```
[filament_switch_sensor filament_sensor] # filament sensor
switch_pin: !PC6
pause_on_runout: true
```

And replace them with:

```
[filament_motion_sensor filament_sensor]
detection_length: 5.3
extruder:extruder
pause_on_runout: true
switch_pin: ^PC6
runout_gcode:
  RESPOND TYPE=command MSG="Filament runout/blocked!"
insert_gcode:
  RESPOND TYPE=command MSG="Filament inserted"

```

**Or** add audio alerts — several beeps will sound when paused:

```
[gcode_shell_command beep]
command: beep
timeout: 2
verbose: False

[gcode_macro BEEP]
description: Play a sound
gcode:
  RUN_SHELL_COMMAND CMD=beep

[filament_motion_sensor encoder_sensor]
switch_pin: ^!PC6
detection_length: 5.3 # can possibly be reduced slightly — 2.7 is the default
extruder: extruder
runout_gcode:
  RESPOND TYPE=command MSG="Filament runout/blocked!"
  UPDATE_DELAYED_GCODE ID=sfs_alarm DURATION=1
insert_gcode:
  RESPOND TYPE=command MSG="Filament inserted"
  UPDATE_DELAYED_GCODE ID=sfs_alarm DURATION=0


[delayed_gcode sfs_alarm]
# initial_duration: 0
gcode:
  beep
  beep
  beep
  beep
  beep
  beep
  #UPDATE_DELAYED_GCODE ID=sfs_alarm DURATION=1 # uncomment this line for continuous alerts
```
## Method 2

If you want full sensor functionality with fewer false positives, you can use all 4 sensor wires. You will need to route an additional wire.

*In effect, the SFS housing contains 2 sensors: a filament presence sensor and a filament motion sensor. That is why we need the fourth wire.*


First, I connected the included connector via an adapter *(in a roundabout way)*:

![](/images/sfs_connector.png)

The remaining **blue** wire needs to be routed to the motherboard and soldered to this pin. In the photo it appears red because the original wire was about 10 cm too short and had to be extended.

![](/images/sfs_soldering.png)

Open `printer.cfg` and find the lines:

```
[filament_switch_sensor filament_sensor] # filament sensor
switch_pin: !PC6
pause_on_runout: true
```

And replace them with:


```
[gcode_shell_command beep]
command: beep
timeout: 2
verbose: False

[gcode_macro BEEP]
description: Play a sound
gcode:
  RUN_SHELL_COMMAND CMD=beep

[filament_switch_sensor filament_sensor]
switch_pin: PA15
pause_on_runout: true
# runout_gcode: PAUSE



[filament_motion_sensor encoder_sensor]
switch_pin: ^!PC6
detection_length: 5.3
extruder: extruder
runout_gcode:
  RESPOND TYPE=command MSG="Filament runout/blocked!"
  UPDATE_DELAYED_GCODE ID=sfs_alarm DURATION=1
insert_gcode:
  RESPOND TYPE=command MSG="Filament inserted"
  UPDATE_DELAYED_GCODE ID=sfs_alarm DURATION=0


[delayed_gcode sfs_alarm]
# initial_duration: 0
gcode:
  beep
  beep
  beep
  beep
  beep
  beep
```
### Installation

To install, you need to replace the filament sensor platform. Download the model [**from here**](/files/Ender5MaxSFSMount.stl)

It looks like this:

![](/images/sfs_mounted.png)