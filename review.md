First impressions of the printer.

1. The box is huge and heavy — 31 kg. Sturdy, powerful, impressive. Liked it.

2. Assembly is fairly easy. Watched a [video](https://youtu.be/VDOPQRVCY3I?si=ck5SxVFJXiyOnVK2) and also used the included assembly guide. All bolts are in separate labeled bags. Very convenient.

3. The first boot takes a while — while the test runs, the bed moves up and down more slowly compared to the K series, but that's not a big deal.

4. The dimensions listed on the website look daunting, but the actual metal cube measures 608×608×625 mm; the rest is protrusions such as the front screen, filament sensor, filament holder on the right, and the cable carrier above going to the print head.

The stock bed mesh at 45°C looks like this:

![](/images/map1.jpg) 

However, the first layer hardly disappointed and looks very decent — especially impressive for a 400×400 mm bed:

![](/images/first_layer.jpg)

![](/images/first_layer2.jpg)


3 The power consumption graph for the bed at 45°C and hotend at 220°C is quite reasonable.

![](/images/watt.jpg)

 Keep in mind that the bed heater here is 1000 W, and the hotend also looks quite impressive.
 
 
![](/images/hotend.jpg)
 

The nozzle here is proprietary and specific — similar to MK8 but with a longer tip.


 The hotend during tests easily pushed 30 mm³/s. At 35 minor defects appeared, but brief bursts up to that level are possible.

 ![](/images/flowtest.jpg)



4 A few photos of what the menu looks like. Right off the bat — at this point there was no root access. (root access now available)

![](/images/menu1.jpg)

![](/images/menu2.jpg)

![](/images/menu3.jpg)

![](/images/menu4.jpg)

![](/images/menu5.jpg)

![](/images/menu6.jpg)

![](/images/menu7.jpg)

Nothing new for Creality users — cloud service access works out of the box as usual.

From experience, purging new filament is best repeated two or three times because the hotend volume is significantly larger now, and the color may take a while to mix out from the previous one.

The [Creality Nebula Camera](https://aliexpress.ru/item/1005006159528565.html) would be a great addition — how well the AI will perform is yet to be seen, but even without AI it's a useful tool for monitoring prints.

## Update. 

After obtaining root access, in response to the question of how "terrible" the printer prints stock:

1. Creality, as always, tries to sweep the actual bed warp values under the rug. When printing many small parts it's not a big deal, but if you start printing a large part that needs to mate with another large part, this can become a very serious problem.
2. The lack of object exclusion becomes a problem when printing many small models.
3. The bed strain gauges don't work perfectly. I got lucky and my bed was leveled reasonably well from day one (compared to the K1), but comparing it to the Neptune 4 Max (which costs half the price) the first layer there impressed me more. Keep in mind that Creality did not provide us with any tools to correct the bed.
4.  `variable_autotune_shapers: ei` — as has been said time and again... Forcing a limited choice of shapers does not improve print quality; it actually makes things worse.
5. No start and end g-code, no KAMP.

6. According to user reports, one of the biggest issues with the stock firmware is an error that causes incorrect return height when resuming from a pause at a layer.

At the moment, one of the most refined firmware versions is by pellcorp with his [**SimpleAF**](https://pellcorp.github.io/creality-wiki/).

[**Installing SimpleAF firmware (in English)**](/mans/simpleaf.md) — its only downside is that it can be installed **only** with Cartographer or Beacon probes. This is because the updated Klipper could not communicate with the toolhead board's accelerometer. Since Cartographer and Beacon have their own built-in accelerometers, this workaround was used. It is quite possible that the situation will change in time.

I am also modifying the firmware and adding capabilities to the stock configuration. Since the user base is still small, the community is not as active as one would like.


