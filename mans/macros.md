<h3 align="right"><a href="https://www.tinkoff.ru/rm/yakovleva.irina203/51ZSr71845" target="_blank">your "thank you" to the author</a></h3>
<h3 align="right"><a href="https://t.me/tombraider2006" target="_blank">author's Telegram channel</a></h3>
<h5 align="right">give the project a "star" so other users can find it more easily.</h5>


## List of Useful Macros with Explanations

After installing the helper script and opening your web panel, you may see a huge "wall" of all kinds of macros. Most of them are system macros and are not intended for direct interaction — they are from the system, for the system, and about the system. To avoid confusion and clutter in the interface, you can perform a few simple operations:

1 — open settings

2 — click Macros

3 — add a category

4 — enter the category name (mine is "tests")

5 — click Save

![](/images/macros1.png)

Now go to the "Uncategorized" section and, following the list below, select each macro, click on it, and assign it to the category you just created:

![](/images/maros2.jpg)


`BELTS_SHAPER_CALIBRATION` — runs a resonance test and generates a belt tension graph.

`EXCITATE_AXIS_AT_FREQ` — a resonance testing macro at a specific frequency to detect assembly defects.

![](/images/macros3.jpg)

In the image above, we can see the default values — shaking the X axis at 25 Hz for 10 seconds. For the test, set 3–5 seconds and sweep through frequencies 25, 27, 30, etc. to find the frequency of maximum vibration. Then set the test duration to 30–40 seconds and feel around the frame to locate the resonating component. A visual inspection will reveal the problem (a loose bolt, a detached panel, an under-tightened screw, etc.).

`INPUT_SHAPER_CALIBRATION` — runs a resonance test and writes the result to `printer.cfg`.

In the stock firmware, shaper selection is limited to `ei`, which is far from optimal. Helper Script resolves this issue, so after installing the script, use this macro to set the correct shapers.

`TEST_RESONANCES_GRAPHS` — runs a resonance test and saves the results as a graph.

The graph makes it easier to understand kinematic issues, choose maximum accelerations in the slicer, and verify how accurately the auto-calibrator has set values in `printer.cfg`.

After the test completes, the graphs will be in the folder: `/Helper-Script/improved-shapers`.

For SimpleAF firmware, the graphs will be in the `/images` folder.


`PID_BED` — a calibration procedure that ensures the printer always maintains a stable target temperature. PID (Proportional-Integral-Derivative) is used in printers to maintain stable bed temperature. By default the test runs at 70°C, but next to the icon there is a dropdown arrow — expand it and set the temperature you normally print at.


`PID_HOTEND` — a calibration procedure that ensures the printer always maintains a stable target temperature. PID is used in printers to maintain stable hotend temperature. By default the test runs at 250°C, but next to the icon there is a dropdown arrow — expand it and set the temperature you normally print at.

`TILT_TABLE` — automatic bed tilt adjustment.


**ONLY** for Simple AF firmware on Ender 5 Max.

Algorithm:

1. Home all axes
2. Rapidly move down to Z=400 (not all the way to the bottom)
3. "Trick" the Z height — pretend Z is 370 instead of 400
4. Slowly move down = the satisfying crunch ****
5. Another "trick" (the final check)
6. Even slower — a bit more crunching ***
7. Rapidly move up — almost home, but not quite...
8. Home Z (standard Z homing)

Copy the macro to any location in printer.cfg.
Save and restart Klipper.
Then in the "Macros" section you will see the new "tilt_table" entry.
If desired, you can rename this button to a more descriptive name in the macro settings and assign any color (marker) to the button.
If the bed speed bothers you, you can decrease or increase the value (F1300).

```
[gcode_macro tilt_table]
gcode:
    G28
    G0 Z400 F1300
    SET_KINEMATIC_POSITION Z=370 
    G0 Z400 F400
    SET_KINEMATIC_POSITION Z=390
    G0 Z400 F300
    G0 Z20 F1300
    G28 Z
```



After this, your web panel will have a cleaner look and the macros you need will be displayed more clearly.

For Simple AF firmware, everything described above also applies, though the macro names are slightly different.




![](/images/macros5.jpg)
