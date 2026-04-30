Flashing the Cartographer on Windows (currently unverified — in practice, Linux is simpler)

## Entering DFU Mode

Entering DFU mode can be a bit tricky, but once you get the hang of it, it becomes fairly simple.

Connect the USB cable to the Windows PC you will be flashing from, and to the Cartographer sensor.

Using metal tweezers or a similar metal tool, short the contacts at pin 1 (boot0). Once a solid contact is made, touch pin 2 (reset) with another metal tool.

![](/images/image_plate_dfu.png)


If you did everything correctly, your device should enter DFU mode.

On Windows — follow these steps:

Start Menu

Find and open Device Manager.

Scroll down to Universal Serial Bus devices.

You should see STM32 BOOTLOADER as an option.

![](/images/image_win_dfu.png)


## Flashing via STM32CubeProgrammer on Windows 

The program can be downloaded from the [official website](https://www.st.com/en/development-tools/stm32cubeprog.html) but only via VPN and with registration. Alternatively, use the link from the Telegram group of [Ender 5 Max users](https://t.me/Ender_5_Max_Ru/5283).

Open the application, select the following options on the RIGHT side, and click Connect.

![](/images/image_stm32_dfu.png)

To flash the Cartographer, you need to set the address `0x08002000`. This provides an 8 KB offset for the firmware. The Katapult bootloader firmware can be flashed at the default address `0x08000000`.

![](/images/stm32_dfu_util.png)

For each firmware file, click "Download" — starting with Katapult, then Cartographer. Then click Disconnect in the UPPER RIGHT corner.

If the BLUE LED is blinking, you have not completed the firmware update and should start over. If you power cycle the probe or simply touch the RESET contacts (2) as before, the probe should respond when something hard and metallic is placed beneath it.