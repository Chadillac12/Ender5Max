<h2>Printer Configuration Fixes for Extended Functionality.</h2>

Open printer.cfg

 1. Find the `[printer]` section:

```
[printer]
kinematics: corexy
max_velocity: 800
max_accel: 20000
max_accel_to_decel: 10000
max_z_velocity: 30
square_corner_velocity: 5.0
max_z_accel: 300

```
By correcting it as follows, we move away from incorrect maximum acceleration values which — with wrong slicer settings — could damage the printer.

 2. After that section, add this line:

```
[exclude_object]

```
This adds object exclusion functionality, which is an extremely useful feature for printers with a build plate this size.

 3. In the `[adxl345]` section, the line `axes_map: x,-z,y` is definitely incorrect, but the correct value has not yet been confirmed.

 4. The printer status LED is reportedly too bright. Let's fix that: find the following sections and change them as described here. We also fix the bed lighting logic which was inverted.

 ```
[output_pin green_pin]
pin: PA0
pwm: True
cycle_time: 0.010
value: 0
[output_pin red_pin]
pin: PC1
pwm: True
cycle_time: 0.010
value: 0
[output_pin yellow_pin]
pin: PA1
pwm: True
cycle_time: 0.010
value: 0.01
[output_pin light_pin]
#pin: nozzle_mcu: PB0 #PA10
pin: !PC0
pwm: True
cycle_time: 0.010
value: 1.0
 ```
 5. For the algorithms to work correctly, we also need to fix the `gcode_macro.cfg` file. Open it and find the following sections and replace them with the ones shown below:

```
###YINZHI
[gcode_macro RED_LED_ON]
gcode:
  SET_PIN PIN=red_pin VALUE=1

[gcode_macro RED_LED_OFF]
gcode:
  SET_PIN PIN=red_pin VALUE=0

[gcode_macro GREEN_LED_ON]
gcode:
  SET_PIN PIN=green_pin VALUE=0.02

[gcode_macro GREEN_LED_OFF]
gcode:
  SET_PIN PIN=green_pin VALUE=0

[gcode_macro YELLOW_LED_ON]
gcode:
  SET_PIN PIN=_yellow_pin VALUE=0.01

[gcode_macro YELLOW_LED_OFF]
gcode:
  SET_PIN PIN=yellow_pin VALUE=0

[gcode_macro LIGHT_LED_ON]
gcode:
  SET_PIN PIN=light_pin VALUE=1

[gcode_macro LIGHT_LED_OFF]
gcode:
  SET_PIN PIN=light_pin VALUE=0
```
I left the red LED unchanged so that it draws attention when an error occurs. However, you can also adjust its brightness.

 6. To make the printer a bit quieter, I changed the logic of the motherboard fan — it now activates when the X-axis motor is enabled.
```
#############FAN OLD CONFIG
#[output_pin MainBoardFan] # comment out this section
#pin: !PB1

[controller_fan MCU_fan] # enable fan when stepper drivers are active
pin: PB1
max_power: 1.0
fan_speed: 1
kick_start_time: 0
stepper: stepper_x
```
In this example you can see what to comment out and what to add.
