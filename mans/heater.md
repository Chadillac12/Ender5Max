# Active Heated Chamber for Ender 5 Max

After installing the stock enclosure, the desire to print high-shrinkage filaments (ABS, PA, etc.) naturally arises.

The second issue is printing with standard filaments — removing and replacing the large lid is quite inconvenient, especially in tight spaces.

We will try to solve both of these problems by building additional equipment.

1. Chamber heater — designed to speed up and stabilize the temperature inside the printer.

2. Additional exhaust fans — to provide airflow of hot air when needed. This is especially important when printing low-temperature filaments such as PLA, PETG, and similar.

Since the printer's motherboard has limited connectors and pins, we need to add an additional MCU board to handle all of this.

## Materials and Tools

1. Additional MCU board — [expander MCU Voron](https://aliexpress.ru/item/1005002394568889.html)
2. [Gdstime GDB 12032 Plastic Fan 12cm 24V](https://aliexpress.ru/item/1005002311794444.html)
3. [Cooling fan DC12025HBL 24V, 120x120x25mm, ball bearing — **2 pieces**](https://www.ozon.ru/product/ventilyator-ohlazhdeniya-kuler-dc12025hbl-24v-120-120-25mm-podshipnik-kacheniya-1990050205/)
4. [10A SSR-10DA solid-state relay](https://www.ozon.ru/product/avtomaticheskiy-vyklyuchatel-10a-1661490786/)
5. [PTC Ceramic Constant Temperature High Voltage Heater 220V 400W](https://www.ozon.ru/product/ptc-nagrevatel-keramicheskiy-postoyannoy-temperatury-vysokogo-napryazheniya-220-v-400-vt-2164837347)
6. [Thermistor 100k 3950 — 2 pieces](https://aliexpress.ru/item/1005006645736045.html)
7. 4010 fan 5V
8. [Heat-set inserts 100PCS, M3xL4xOD4.5](https://aliexpress.ru/item/1005006079067094.html)
9. M4×40 screws (8 pcs) with nuts (8 pcs)
10. [24AWG wire](https://aliexpress.ru/item/1005004336218242.html)
11. Steady hands (optional)
12. Lots of motivation (required)

## Print Models

1. [Cap stops — used during maintenance when working with filament loading.](/files/cap_stop_v2.stl)
2. [Chamber heater housing](/files/chamber_heater_v16.stl) — split into separate models in the slicer and arrange before printing.
3. [Back lid panels for mounting fans — print 2 pieces](/files/back_kolpak_half.stl)
4. [Fan shutters/louvres](https://www.printables.com/model/9054-gravity-vent-for-enclosure-for-120mm-fan)
5. Magnetic connector for the lid contact connection
6. [Rear cover of the extruder head body](/files/backcover.stl)
7. [Drilling template for the rear panel](/files/holes_v16_mirror.pdf)

## Assembly

I tried printing in black ABS and realized that photos of the assembly would show black on black. So for the manual I deliberately printed in light ABS where possible to make things more visible.

### Heater housing

![](/images/first_view.jpg)

1. Using heat-set inserts, assemble the housing.

![](/images/nuts_insert.jpg)

Don't forget the back side:

![](/images/nuts_insert2.jpg)



1.2. Wire the heater and relay. The photo makes it easy to understand.

![](/images/heater_assemble1.jpg)

2. The wiring supports 2 options: with a bimetallic auto-shutoff sensor, or with a thermistor connected to the additional MCU board.

![](/images/heater_assemble2.jpg)

2.1. Attach the blower fan. Route the wire slightly differently for a cleaner look.

![](/images/heater_assemble3.jpg)


Connect the remaining wires:

![](/images/heater_assemble4.jpg)

Wiring connections to the printer power supply shown in the photo:

![](/images/BP.png)

Cross-reference or note down where each wire goes. The pinout diagram will help:

![](/images/pinout_expander.png)

**Power Input** — our 24V power supply input.

**PA0** — Wire to the heater relay.

**PA1** — Wire to the heater blower fan.

**PA3** — Wiring to the cooling fans installed in the lid.

**PA5** — Heater thermistor. Optional but recommended. If your heater has a bimetallic sensor, you don't need this.

**PA6** — Chamber thermistor — the main one.

3. To mount to the rear wall, use the [**template**](/files/holes_v16_mirror.pdf) for drilling the rear panel.

Print it and apply masking tape to the edge as shown in the photo.

![](/images/blueprint1.jpg)

Carefully place it on the rear wall flush with the lower left corner and drill through the marked holes. You did insert the heat-set inserts from the back, right?

![](/images/blueprint2.jpg)

I drilled with a 4.5mm bit to allow some tolerance for imperfect printing or imprecise drilling. I got lucky — everything lined up.

![](/images/holes_inside.png)

Attach the heater housing.



4. For easier wiring, don't rush to screw on the additional MCU cover — you can do that after confirming everything works.
5. The USB Mini-USB cable (don't rush to buy it before the board arrives — it may come as either **Type-C** or **Mini-USB**) at least 1.5m, preferably 1.8m, should be routed from the screen through the cable opening, then inside the right upright of the printer, and along the bottom right profile to the rear wall.
6. I attached the chamber thermistor with double-sided tape above the heater.

![](/images/termistor_glue.jpg)

The second thermistor is in contact with the heater itself and serves purely for my peace of mind and reference — but the config specifies that if it heats above 140°C, the printer will shut down.




### Installing the Rear Panel on the Lid
1. Remove the rear panel from the original lid and screw on the printed models with fan cutouts.
2. The fans have an arrow showing airflow direction — referencing it, assemble the fan body and louver housing with M4×40 screws and nuts (8 pieces).
3. Using the screws that were transport fasteners for the Z-axis side uprights (you didn't throw them away, right?), replace the lower side screws with them and attach the side stops. You need 2 M4 nuts.

4. Wire the magnetic connector (holder model in development) and connect it to the board inside the heater housing.


### Replacing the Toolhead Rear Cover

When the air inside the printer chamber heats up, the control board inside the print head starts to cool less effectively. To fix this we need to install an additional fan, wired in parallel with the hotend heatsink cooling fan (temporary — a connection to the rear board is being explored). The housing should ideally be printed in high-temperature filament; suitable options are:
1. PA-CF or PA-GF
2. ABS-CF or ABS-GF
3. As a last resort, ABS or HIPS

*Honestly, the placement of the Mcu_nozzle chip is beyond me logically. Not only is it mounted at the back, it also faces the motor body — making it impossible to add a heatsink or cool it properly.*



### Configuration

1. Download the [heater.cfg](/files/heater.cfg) file and place it in the printer config folder.

2. Open printer.cfg and add the following line on a new line anywhere in the file:

`[include heater.cfg]`

3. To also update the screen menu, add a few lines to `/usr/data/guppyscreen/guppyscreen.json`.

    * As a good practice, download the original file as a backup first:

    ```
    cp /usr/data/guppyscreen/guppyscreen.json /usr/data/guppyscreen/guppyscreen.json.backup
    ```

![](/images/guppy1.png)

```
,
    {
      "display_name": "back_fans",
      "id": "fan_generic back_fans"
    },
    {
      "display_name": "Chamber",
      "id": "fan_generic chamber"
    }
```


Then find the following lines as shown in the picture and insert similarly to the first example:

![](/images/guppy2.png)

```
,
    {
      "color": "blue",
      "controllable": true,
      "display_name": "Chamber",
      "id": "heater_generic Heater_TT"
    },
    {
      "color": "green",
      "controllable": true,
      "display_name": "Blower_Fan",
      "id": "temperature_fan blower_fan"
    }
```
