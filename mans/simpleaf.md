
## Installing Simple AF Firmware on Ender 5 Max

### Why Simple AF?

This modification is designed to solve several problems that exist in the stock firmware and cannot be resolved even with Helper Script modifications.

The slow startup and slow, inaccurate initial Z-axis test, nozzle wipe, and the "round trip" strain gauge test. In Simple AF firmware, the native strain gauges are not used — alternatives are offered instead.

The absence of the KAMP algorithm in the stock firmware means the bed is probed slowly, inaccurately, and across the entire surface rather than only where needed.

Falling behind modern trends in algorithm development — Input Shaping, Adaptive Mesh, and much more have evolved, unlike the firmware which has remained in the same state as it was several years ago! Cosmetic improvements have not addressed the gap and in some cases introduced new problems, such as the hard dependency on Creality Print starting from firmware 1.2.0.20.

All of this we are forced to accept because Creality chose their own path for Z-axis probe development, which diverged from the main Klipper branch.

A few notes from the firmware site about the advantages of this solution.

*Open source — Simple AF uses open-source software and all its source code is open.*

*Simple configuration — All macros and configurations are in the config directory and are accessible to you; nothing is locked in closed source code.*

*Updated Klipper — The version of Klipper we use, while a fork of the main branch, is regularly updated from klipper3d, and none of our additions change the base Klipper behavior.*

The main issue is that the updated Klipper in this firmware cannot use the stock bed strain gauges — they must be abandoned. One of the most advanced alternatives is the Cartographer probe, which we will pair with our printer. First we need to gain access to the Linux console, and for that we need to install the pre-built firmware from [zevaryx](/firmware.md) which will allow us to connect via SSH with root privileges.

Next we need to prepare the printed mounting parts for the Cartographer probe and screen:

### Probe Mounting Options

| Model        | Type            | Link                                                                  | Notes                               |
|--------------|-----------------|-----------------------------------------------------------------------|-------------------------------------|
| SimplyHexed  | 90-degree angle  | [Printables](https://www.printables.com/model/1209230-ender-5-max-simply-hexed)                              | Ender 5 Max only                    |


If you replaced the hotend with the K2 hotend, you need a [blower housing reduced by 2mm in height](/files/front_cover_carto.stl)

### Screen Mount

Since SimpleAF firmware uses horizontal screen orientation, use [**this**](/files/Ender5MaxScreenMount.stl) mounting model.

### Flashing the Probe

Unfortunately, flashing the probe directly from the printer is not possible — the Linux on the printer is heavily stripped down, so you will need to use a workaround such as a Live USB with Ubuntu.

The probe must have firmware no older than `CARTOGRAPHER K1 5.1.0`:

[**Probe flashing instructions**](https://pellcorp.github.io/creality-wiki/cartographer_flashing/)

[**Probe flashing instructions on Windows using WSL Hack**](/mans/wsl.md) — flashing using the Linux subsystem built into Windows.

#### Flashing Errors

You can always check whether the probe is visible in the system with the command `ls /dev/serial/by-id/*`. If the system finds nothing, check the connection. Sometimes the data transmission wires (green and white) are swapped — try swapping them.

![](/images/carto_usb.png)

If everything is correct, the system should output something like `/dev/serial/by-id/usb-Klipper_stm32f042x6_120031001653304B4D323431-if00` as a result of `ls /dev/serial/by-id/*` — *this is just an example, do not use it*.

Later, the installer script uses this string in the `cartotouch.cfg` file to identify the device, and it will look something like this:

```
[scanner]
is_non_critical: true           # flag this as non critical
serial: /dev/serial/by-id/usb-Cartographer_614e_28001D00124330394D363620-if00
```


### Preparation and Flashing

To make sure nothing interferes and no leftover settings remain, we will reset the firmware to factory defaults while preserving Wi-Fi access and settings. Copy this code and paste it into the console:

```
wget --no-check-certificate https://raw.githubusercontent.com/pellcorp/creality/main/k1/services/S58factoryreset -O /tmp/S58factoryreset
chmod +x /tmp/S58factoryreset
/tmp/S58factoryreset reset

```
It is very important not to close the SSH session until you receive the following message:

`Info: Executing factory reset... Info: Factory reset was executed successfully, the printer will restart...`

The printer will reboot and you can reopen the SSH console.

### Important! Check the date on the printer.

```
date
```
If it differs from the current time:

```
hwclock -w
```
Then check again:


```
date
```
**Option 2.**

 Simply set the current timezone on the printer.

If this is not done, there will be certificate errors and the installation modules will not download.


Next, clone the firmware repository:

```
git config --global http.sslVerify false
git clone https://github.com/pellcorp/creality.git /usr/data/pellcorp
sync

```
**Alternative server** if there are errors due to access being blocked:

```
git config --global http.sslVerify false
git clone https://tomich.fun/git/creality.git /usr/data/pellcorp
sync
```

### Run the Installer

```
/usr/data/pellcorp/installer.sh --install cartographer --mount SimplyHexed

```

The installation will take some time.

### After Installation

Possible MCU Firmware Update

If at the end of the installation process you see the following message:

`WARNING: MCU Firmware updates are pending you need to power cycle your printer!`

This means new MCU firmware updates need to be applied, and this can only be done by power cycling the printer. After power cycling, you can verify the firmware was updated using the `CHECK_FIRMWARE` macro from Fluidd. If you see: `INFO: Your MCU Firmware is up to date`, everything is fine.

After flashing, the printer screen will prompt you to calibrate the screen. Do not ignore this.

### Calibration

To set up a new printer, three separate calibration steps are required:

<details><summary>Manual Cartographer calibration<small><i><red> click to expand</red></i></small></summary>

Run the following commands in the web panel console.

1. Home XY axes: `G28 X Y`
2. Heat the nozzle to 150°C: `M109 S150`
3. Make sure the nozzle is positioned at the center of the bed.
4. Run `CARTOGRAPHER_SCAN_CALIBRATE` — paper test. Do not use a metal feeler gauge at this step, it may interfere with calibration!!!
5. When finished, click Save and Restart, or enter `SAVE_CONFIG` in the panel. </details>

<details><summary>Cartographer touch threshold scan<small><i><red> click to expand</red></i></small></summary>

**At this next step it is very important to stay near the printer, because if there are any problems with the printer configuration or the Cartographer probe, the nozzle may crash into the bed — so keep your hand over the emergency stop button!**

Run the following commands in the web panel console.

1. Home all axes: `G28`
2. Make sure the nozzle is positioned at the center of the bed.
3. Heat the nozzle to 150°C: `M109 S150`
4. Run `CARTOGRAPHER_TOUCH_CALIBRATE`
5. When finished: `SAVE_CONFIG`
</details>



The original manual recommends running axis twist compensation calibration next, but since our mount has no offset on either axis, this is optional.

If your bed mesh looks like this:

![](/images/map_carto.png)

then you forgot to flash the Cartographer itself and will need to repeat all the calibration steps after flashing.

 If you are running calibration on a printer that was previously calibrated, you need to remove the following SAVE_CONFIG sections before repeating the calibrations:
```
[scanner model default]
[scanner]
[axis_twist_compensation]
[bed_mesh]

```




### Tuning the Firmware

Let's start with a few tests and calibrations.

Run the following commands in the web panel console:

```
PID_CALIBRATE_BED BED_TEMP=65
PID_CALIBRATE_HOTEND HOTEND_TEMP=230
INPUT_SHAPER
SAVE_CONFIG
```

`PID_CALIBRATE_HOTEND HOTEND_TEMP=230` and `PID_CALIBRATE_BED BED_TEMP=65` are calibration procedures that ensure the printer always maintains a stable target temperature. PID (Proportional-Integral-Derivative) is used in printers to maintain stable temperatures in the hotend and/or heated bed.

`INPUT_SHAPER` runs a resonance test and writes the result to `printer.cfg`.

When done, `SAVE_CONFIG` will save all our data to `printer.cfg` and restart the printer.

You can also run `INPUT_SHAPER_GRAPHS` to view the resonance graphs. After the test they will appear in the root config folder.

`BELTS_SHAPER_CALIBRATION` runs a resonance test and generates a belt tension graph.

## Editing Start and End G-code in the Slicer

Modern printers have long moved away from start G-code in the slicer and instead use macros that, at the start of printing, check various conditions and prepare the printer for printing much more effectively. To use these modern methods in our firmware, simply set the correct start and end G-code in the slicer.

Since we are using firmware retraction, we need to adjust a few settings:

![](/images/orca1.png)

![](/images/orca2.png)

Start G-code for **OrcaSlicer**:

```
M140 S0
M104 S0
START_PRINT EXTRUDER_TEMP=[nozzle_temperature_initial_layer] BED_TEMP=[bed_temperature_initial_layer_single]
SET_RETRACTION RETRACT_LENGTH=[retraction_length] RETRACT_SPEED=[retraction_speed] UNRETRACT_EXTRA_LENGTH=[retract_restart_extra] UNRETRACT_SPEED=[deretraction_speed]
RESPOND TYPE=command MSG="Retraction length set to [retraction_length]mm" 
RESPOND TYPE=command MSG="Retract speed set to [retraction_speed]/[deretraction_speed]mm/c"

```

End G-code for **OrcaSlicer**:

```
END_PRINT

```
 If after installing Simple AF the printer is printing incorrectly and you are getting "Unknown command G2, G3, G10, G11", add this to the end of printer.cfg somewhere:

 ```
[gcode_arcs]

[firmware_retraction]
retract_length: 0.45 # safe value for the filament you print with most often
retract_speed: 30
unretract_extra_length: 0
unretract_speed: 30

 ```

### You bought the enclosure and now the wires are blocking the door from opening.

Here is the [**model**](/mans/Ender5MaxTiltedScreenMount.stl) to mount the screen in a horizontal position.


![](/images/door_lock_screen.png)


### Changing Screen Orientation:

1. In the Fluidd files, find grumpyscreen.ini.

Look for the parameter:

`"display_rotate": 2,` — find this line and change the value to 0.


2. **Reboot and run screen calibration.**


![](/images/door_unlock_screen.png)


You can contact me for [**consultation**](/kurs.md) and I will help with the software installation.

## Miscellaneous

To stop the printer's motherboard fan from running noisily at idle.

In the file `fan_control.cfg`, **replace** the `[static_digital_output mcu_fan_always_on]` section. Delete the old one and insert this:

```
[controller_fan MCU_fan] # enable fan when stepper drivers are active
pin: PB1
max_power: 1.0
fan_speed: 1
kick_start_time: 0
stepper: stepper_x

```

In the file `start_end.cfg`, find the macro `[gcode_macro _CLIENT_VARIABLE]`.

In it, change 25 to 380 in the line below so the bed moves lower after printing. *Useful for those whose printer is mounted above floor level.*

```
variable_custom_park_dz: 380.0 # bed position after printing
```
By request, a bit more translation from the macro `[gcode_macro _CLIENT_VARIABLE]`:


```
[gcode_macro _CLIENT_VARIABLE]
variable_park_at_cancel: True # do not change
variable_use_custom_pos: True # do not change
variable_custom_park_dz: 380.0 # Z position on cancel or print end
variable_park_at_cancel_x: 396 # X position on cancel or print end
variable_park_at_cancel_y: 391 # Y position on cancel or print end
variable_cancel_retract: 7.0 # filament retract on cancel or print end
variable_custom_park_x: 300 # X position on pause, filament change, or runout
variable_custom_park_y: 9 # Y position on pause, filament change, or runout
variable_retract: 1.0 # default retract value
variable_speed_retract: 35.0 # retract speed in mm/s
variable_unretract: 1.0 # unretract speed in mm/s
variable_speed_unretract: 35.0 # unretract feed speed in mm/s
variable_speed_hop: 15.0 # Z-axis movement speed in mm/s
variable_speed_move: 100.0 # travel speed in mm/s
variable_user_cancel_macro: "_ON_CANCEL" # do not change — handles print cancel
variable_user_resume_macro: "_ON_RESUME" # do not change — handles resume after pause
variable_user_pause_macro: "_ON_PAUSE" # do not change — handles custom code on pause

```

If you don't want to wait for temperature stabilization before printing, set 0 in the following macro:

```
[output_pin Bed_Warp_Stabilisation]
pin: virtual_pin:BED_WARP_STABILISE_pin
value: 0

```


