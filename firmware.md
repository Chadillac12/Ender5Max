<h3 align="right"><a href="https://www.tinkoff.ru/rm/yakovleva.irina203/51ZSr71845" target="_blank">your "thank you" to the author</a></h3>
<h3 align="right"><a href="https://t.me/tombraider2006" target="_blank">author's Telegram channel</a></h3>

<h2>Firmware</h2>


At the moment, `root` access can be obtained using firmware [1.2.0.21](https://www.crealitycloud.com/downloads/firmware/ender-series/ender-5-max) with the password `creality_2024`

![](/images/root1.jpg)

![](/images/root2.jpg)

![](/images/root3.jpg)




<details><summary>or using the modified firmware https://github.com/zevaryx/ender-5-max-firmware/releases/latest (outdated)</summary>

with the password `creality_2025` 

If simply copying the file to a flash drive, inserting it into the printer, and agreeing to the update doesn't work, and you previously had an earlier firmware with `root` access, you can try to force the update:

```
/etc/ota_bin/local_ota_update.sh /tmp/udisk/sda/*.img
```

or


```
/etc/ota_bin/local_ota_update.sh /tmp/udisk/sda1/*.img
```
After the update, you must power cycle the printer — this is mandatory!


## Rolling Back to a Previous Firmware

To reset the firmware, install an older version, or return to the stock firmware

connect via SSH and run the following commands:

```
# Get the current version, whatever it is
VERSION=$(grep 'ota_version' '/etc/ota_info' | awk -F'=' '{print $2}')

# Force version to 1.0.0.0, which is below the minimum
for file in /etc/ota_info /usr/data/creality/userdata/config/system_version.json; do
    sed -i -e "s/${VERSION}/1.0.0.0/g" $file
done

# Reboot the system to force a file reload from disk
reboot
```
After this, you can supply any firmware to the system and it will accept it as new.
</details>
## Installing HELPER-SCRIPT

### Warning! If you install the helper script it will disable update notifications. Also, do not update the firmware over Wi-Fi afterwards until you have reset the printer to factory settings.


*21.05.2025 — Warning.*

*Guilois, the author of the helper script, has finally paid attention to this printer and created a dedicated branch for it, but it has not been developed further. Therefore you should only install the items that are compatible with our printer — blindly installing everything will cause the printer to throw errors or stop working correctly.*

While waiting for your Cartographer to arrive but already wanting to print, you can make life easier by installing the familiar Klipper shell. To do this, install the [**helper script**](https://guilouz.github.io/Creality-Helper-Script-Wiki/helper-script/helper-script-installation/) and get basic Klipper functionality.



To do this, run the following commands over SSH:

```
git clone --depth 1 https://github.com/Guilouz/Creality-Helper-Script.git /usr/data/helper-script
```
Once the script has downloaded, run it:

```
sh /usr/data/helper-script/helper.sh
```

Select item 1 — install


![](/images/helper_script.jpg)

Recommended menu items: 1, 2, 4, 5, 10. If you have a camera, also select 16.



Check with the [**printer user group on Telegram**](https://t.me/Ender_5_Max_Ru) to confirm the correct items, as they may change without notice.


Through your browser you can now access the printer's extended web panel with access to configuration files and advanced settings. Remember to specify the port: `http://your_ip:4408` if you installed Fluidd, or `http://your_ip:4409` if Mainsail.

## After Installation

## NEW 
### Automatic patching option

1. Navigate to the printer config folder. Download and run the fix_ender5.sh script from GitHub:

```
wget -q --no-check-certificate https://raw.githubusercontent.com/Tombraider2006/Ender5Max/refs/heads/main/files/fix_e5m.sh -O /tmp/fix_e5m.sh && sh /tmp/fix_e5m.sh
```

<details><summary>Outdated — included in the automatic version.</summary>


In the web panel, find the icon on the left `{...}`, locate the file `gcode_macro.cfg`, open it, and scroll to the very bottom.

Add the following code:

```
[firmware_retraction]
retract_length: 0.45 # safe value for the filament you print with most often
retract_speed: 30
unretract_extra_length: 0
unretract_speed: 30

[gcode_shell_command beep]
command: beep
timeout: 2
verbose: False

[gcode_macro BEEP] # beep sound
description: Play a sound
gcode:
  RUN_SHELL_COMMAND CMD=beep

[delayed_gcode light_init] 
initial_duration: 5.01
gcode:
  SET_PIN PIN=light_pin VALUE=1

[exclude_object] # object exclusion


[gcode_macro PID_BED]
gcode:
  PID_CALIBRATE HEATER=heater_bed TARGET={params.BED_TEMP|default(70)}
  SAVE_CONFIG

[gcode_macro PID_HOTEND] # why wasn't this here before — added it
description: Start Hotend PID
gcode:
  G90
  G28
  G1 Z10 F600
  M106 S255 #S255 
  PID_CALIBRATE HEATER=extruder TARGET={params.HOTEND_TEMP|default(250)}
  M107

```

Save.

Now for a slightly trickier task. Open the file `printer.cfg`.

Find the parameter `[output_pin Height_module2]` and change it to:

```
[output_pin _Height_module2]
```

Find the section:

```diff
[output_pin light_pin]
#pin: nozzle_mcu: PB0 #PA10
pin: PC0
value: 0.0
```
Change it to:

```
[output_pin light_pin] # printer chamber lighting — a bug in Creality's firmware
pin: !PC0
pwm: True
cycle_time: 0.010
value: 1.0
```

To stop the printer's motherboard fan from running noisily at idle, replace the section:

```
[output_pin MainBoardFan]
pin: !PB1
```

Change to:

```
[controller_fan MCU_fan] # enable cooling when stepper drivers are active
pin: PB1
max_power: 1.0
fan_speed: 1
kick_start_time: 0
stepper: stepper_x

```
</details>

To prepare the printer for use, run the calibration tests and save the results to the config. This is easier than it sounds — just copy the code into the printer web panel console. The tests will take about 10–20 minutes, after which the printer will reboot and be ready to use.

```
PID_BED
PID_HOTEND
INPUT_SHAPER_CALIBRATION
```

## OrcaSlicer
Starting from version 2.3.0, OrcaSlicer includes a built-in profile for the Ender 5 Max.

However, there are a couple of things to fix there too.

In the start G-code, add:

```
M140 S0
M104 S0
```
Like this:

![](/images/orca11.jpg)


**In the Extruder section**, I strongly recommend lowering the retract speed from the default 50 mm/s to 30 mm/s — in both places.

This will protect the feeder gears from premature wear.

## Using Firmware Retraction

1. Edit the start G-code by adding this block:

![](/images/start_code.png)

```
SET_RETRACTION RETRACT_LENGTH=[retraction_length] RETRACT_SPEED=[retraction_speed] UNRETRACT_EXTRA_LENGTH=[retract_restart_extra] UNRETRACT_SPEED=[deretraction_speed]
RESPOND TYPE=command MSG="Retraction length set to [retraction_length]mm" 
RESPOND TYPE=command MSG="Retract speed set to [retraction_speed]/[deretraction_speed]mm/c"

```

2. Enable the checkbox here:

![](/images/orca1.png)

3. Then disable the checkbox here:

![](/images/orca2.png)

4. Don't forget to set the retraction value for each filament profile.

![](/images/orca3.jpg)




## View Stock Configuration Files

1.2.0.10 [here](/stock/config_1.2.0.10/)

1.2.0.20 [here](/stock/config_1.2.0.20/)

1.2.0.21 [here](/stock/config_1.2.0.21/)




At the moment (August 2025), one of the most refined firmware versions is by pellcorp with his [**SimpleAF**](https://pellcorp.github.io/creality-wiki/). Its only downside is that it can be installed **only** with Cartographer or Beacon probes. This is because the updated Klipper could not communicate with the toolhead board's accelerometer. Since Cartographer and Beacon have their own built-in accelerometers, this workaround was used.

It is quite possible that the situation will change in time.  

If you want to use guppyscreen in SimpleAF, you need to [rotate the screen 90 degrees.](https://github.com/Tombraider2006/Ender5Max/blob/main/mans/simpleaf.md#%D0%B2%D1%8B-%D0%BA%D1%83%D0%BF%D0%B8%D0%BB%D0%B8-%D0%B7%D0%B0%D1%88%D0%B8%D0%B2%D0%BA%D1%83-%D0%BA%D0%BE%D1%80%D0%BF%D1%83%D1%81%D0%B0-%D0%B8-%D1%82%D0%B5%D0%BF%D0%B5%D1%80%D1%8C-%D0%BF%D1%80%D0%BE%D0%B2%D0%BE%D0%B4%D0%B0-%D0%BC%D0%B5%D1%88%D0%B0%D1%8E%D1%82-%D0%BE%D1%82%D0%BA%D1%80%D1%8B%D0%B2%D0%B0%D1%82%D1%8C-%D0%B4%D0%B2%D0%B5%D1%80%D1%86%D1%83) Here is the [**model**](/mans/Ender5MaxTiltedScreenMount.stl) to mount the screen in a horizontal position.

### If you receive error 2069 (already included in the automatic script)

In the file `printer.cfg`, find and remove the following sections:

```diff
###喷头前面风扇
[output_pin fan0]
pin: !nozzle_mcu:PB15
pwm: True
cycle_time: 0.01
hardware_pwm: false
value: 0.00
scale: 255
shutdown_value: 0.0
[output_pin en_fan0]
pin: nozzle_mcu: PB6
pwm: False
value: 1.0

###喷头后面风扇
[output_pin fan1]
pin: !nozzle_mcu:PA9
pwm: True
cycle_time: 0.01
hardware_pwm: false
value: 0.00
scale: 255
shutdown_value: 0.0
[output_pin en_fan1]
pin: nozzle_mcu: PB9
pwm: False
value: 1.0

```
Replace with:

```

[multi_pin part_fans]
pins:!nozzle_mcu:PB15,!nozzle_mcu:PA9

[multi_pin en_part_fans]
pins:nozzle_mcu:PB6,nozzle_mcu:PB9

[fan_generic part]
pin: multi_pin:part_fans
enable_pin: multi_pin:en_part_fans
cycle_time: 0.0100
hardware_pwm: false
```

In the file `gcode_macro.cfg`, find and remove the following sections:


```diff
[gcode_macro M106]
gcode:
  {% set fan = 0 %}
  {% set value = 255 %}
  {% if params.S is defined %}
    {% set tmp = params.S|int %}
    {% if tmp <= 255 %}
      {% set value = tmp %}
    {% endif %}
  {% endif %}
  {% if params.P is defined %}
    {% set value = (255 - printer["gcode_macro PRINTER_PARAM"].fan1_min) / 255 * tmp %}
    {% set value = printer["gcode_macro PRINTER_PARAM"].fan1_min + value %}
    {% if value >= 255 %}
      {% set value = 255 %}
    {% endif %}
    SET_PIN PIN=fan1 VALUE={value}

    {% set value = (255 - printer["gcode_macro PRINTER_PARAM"].fan0_min) / 255 * tmp %}
    {% set value = printer["gcode_macro PRINTER_PARAM"].fan0_min + value %}
    {% if value >= 255 %}
      {% set value = 255 %}
    {% endif %}
    SET_PIN PIN=fan0 VALUE={value}
  {% else %}
    {% set value = (255 - printer["gcode_macro PRINTER_PARAM"].fan1_min) / 255 * tmp %}
    {% set value = printer["gcode_macro PRINTER_PARAM"].fan1_min + value %}
    {% if value >= 255 %}
      {% set value = 255 %}
    {% endif %}
    SET_PIN PIN=fan1 VALUE={value}

    {% set value = (255 - printer["gcode_macro PRINTER_PARAM"].fan0_min) / 255 * tmp %}
    {% set value = printer["gcode_macro PRINTER_PARAM"].fan0_min + value %}
    {% if value >= 255 %}
      {% set value = 255 %}
    {% endif %}
    SET_PIN PIN=fan0 VALUE={value}

  {% endif %}
  {% if tmp < 1 %}
    {% set value = tmp %}
    SET_PIN PIN=fan0 VALUE={value}
    SET_PIN PIN=fan1 VALUE={value}
  {% endif %}


[gcode_macro M107]
gcode:
  {% if params.P is defined %}
    SET_PIN PIN=fan0 VALUE=0
    SET_PIN PIN=fan1 VALUE=0
  {% else %}
    SET_PIN PIN=fan0 VALUE=0
    SET_PIN PIN=fan1 VALUE=0
  {% endif %}

```
Replace with:

```
[gcode_macro M106]
description: Set Fan Speed. P0 for part
gcode:
  {% set fan_id = params.P|default(0)|int %}
  {% if fan_id == 0 %}
    {% set speed_param = params.S|default(255)|int %}
    {% if speed_param > 0 %}
      {% set speed = (speed_param|float / 255) %}
    {% else %}
      {% set speed = 0 %}
    {% endif %}
    SET_FAN_SPEED FAN=part SPEED={speed}
  {% endif %}

[gcode_macro M107]
description: Set Fan Off. P0 for part
gcode:
  SET_FAN_SPEED FAN=part SPEED=0


[gcode_macro TURN_OFF_FANS]
description: Stop chamber, auxiliary and part fan
gcode:
    SET_FAN_SPEED FAN=part SPEED=0


[gcode_macro TURN_ON_FANS]
description: Turn on chamber, auxiliary and part fan
gcode:
    SET_FAN_SPEED FAN=part SPEED=1

```
At the very top of the file, find and remove these lines:

```
variable_fan0_min: 90 #240 #90
variable_fan1_min: 70 #240 #70
```



![](/images/root1.jpg)

![](/images/root2.jpg)

![](/images/root3.jpg)





