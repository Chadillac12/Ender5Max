# Replacing the Hotend with the K2 Hotend

At the moment there are several issues with the Ender 5 Max hotend:
1. The hotend itself is not available for purchase. Update (now available)
2. No nozzles available for it other than 0.4mm. Update (sometimes hard to find, but they appear occasionally)
3. Due to design specifics, frequent leaks above the hotend on the throat side.
4. K2 nozzles almost always have a hardened steel tip, which allows printing with composites.

To work around these problems, the decision was made to buy a hotend from the Creality K2 printer.

### Issues to Expect During the Transition

1. The heatsinks are incompatible, so you will need to keep the Ender 5 Max (E5M) heatsink.
2. The K2 wires are shorter than the E5M wires.
3. The nozzle tip is slightly shorter — not critical, but worth noting.

![](/images/hotend_compare.jpg)

4. E5M throat diameter is 6mm; the copper insert on the unicorn nozzle is 4.75mm.

### Materials and Tools

1. Copper tube 6mm diameter with 0.5mm wall thickness and 10mm length. Available [**here**](https://prodiel.ru/trubka-mednaya-m2t-6-kh-05-mm-1-metr)
2. Hex key set from the E5M kit
3. Heat shrink tubing
4. K2 hotend assembly. Available at our partner store [**here**](https://creality-3d.ru/catalog/Ender-5-Max)
5. Thermal paste. Perfectionists can apply it to both the tube and the nozzle heatsink.

### Replacement Procedure
1. Remove the assembled hotend. It is sufficient to unscrew 2 screws and disconnect the heater, thermistor, and fan connectors. Unscrew the fan from the hotend.

![](/images/hotend_vid.jpg)

2. On the removed hotend, unscrew the hotend itself from the heatsink, remembering to loosen the screw on the back that clamps the throat.


3. Insert a 10mm piece of copper tube into the heatsink.

![](/images/hotend2.jpg)

4. To speed up the work, I loosened the thermistor mounting on both hotends and replaced the thermistor in the K2 hotend with the one from the E5M. This way the wires are long enough without needing to extend them.

![](/images/hotend1.jpg)


5. Cut the heater wires and join them with the heater wires from the K2.

![](/images/hotend5.jpg)

6. Assemble everything together and reinstall.

7. After powering on, you can run a hotend PID calibration with the new nozzle, for example:

```
PID_CALIBRATE HEATER=extruder TARGET=250
```

### P.S. 

If you have Simple AF firmware installed, you will need to recalibrate your eddy current probe. Use the [front cover model with adjusted probe-to-bed distances](/files/front_cover_carto.stl). Since the nozzle is shorter, an error occurs due to insufficient height from the bed.

### P.P.S

Information for perfectionists.

If you decided to keep the K2 hotend thermistor and want to correctly define the thermistor type, you need to do the following:

Open the file `printer.cfg`.

Before the `[extruder]` section, insert:

```
[thermistor my_thermistor]
temperature1:25
resistance1:260000
temperature2:220
resistance2:738
temperature3:350
resistance3:98
```
In the `[extruder]` section, find the `sensor_type` parameter and change that line to:

```
sensor_type: my_thermistor
```
