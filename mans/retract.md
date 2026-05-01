<h3 align="right"><a href="https://www.tinkoff.ru/rm/yakovleva.irina203/51ZSr71845" target="_blank">your "thank you" to the author</a></h3>
<h3 align="right"><a href="https://t.me/tombraider2006" target="_blank">author's Telegram channel</a></h3>
<h5 align="right">give the project a "star" so other users can find it more easily.</h5>

<h2> Controlling Retraction During Printing</h2>

In Klipper firmware it is possible to control retraction while printing. This is useful if you suddenly notice that your filament has absorbed moisture and is stringing, but you have already printed "N" hours and really don't want to start over. A little preparation work is required for this.

First, in OrcaSlicer go to the printer settings and enable "Use firmware retraction":

![](/images/firm_retract.png)

Disable the "Wipe while retracting" option in the Extruder tab:

![](/images/clear_nozle_orca.png)

Then add the following block to the printer start G-code:

```
SET_RETRACTION RETRACT_LENGTH=[retraction_length] RETRACT_SPEED=[retraction_speed] UNRETRACT_EXTRA_LENGTH=[retract_restart_extra] UNRETRACT_SPEED=[deretraction_speed]
RESPOND TYPE=command MSG="Retraction length set to [retraction_length]mm" 
RESPOND TYPE=command MSG="Retract speed set to [retraction_speed]/[deretraction_speed]mm/c"
```

![](/images/start_code.png)

Don't forget to enter the retraction value for each filament when you configure it.

For example like this:

![](/images/orca3.jpg)

Then open the printer config file printer.cfg and add this section:

```
[firmware_retraction]
retract_length: 0.43 # safe value for the filament you print with most often
retract_speed: 30
unretract_extra_length: 0
unretract_speed: 30

```
After restarting, if you don't see a new section in the upper right corner, click the three dots and look for the following section:

![](/images/fluid1.jpg)

Inside, find the "Retraction settings" section and enable the checkbox:

![](/images/fluid2.jpg)

As a result, a new section will appear in the web panel that allows you to control your retraction during printing.

![](/images/fluid3.jpg)

To verify everything is correct before printing, slice your model, save it to disk, then open the file with a text editor and search for `Move Z Axis up`:

![](/images/test1.jpg)

Confirm that you have the **set_retraction** command with actual numbers, and that further down you have the **G10** command, which means the retract command was sent and will be processed by the firmware.